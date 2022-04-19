# Get cookies from browsers for Windows
This fetches cookies from Chrome, Firefox, Edge, Opera.  
Only Chrome, Firefox and Edge have been tested.  

# Credits:
GramThanos's [GimmeCookies.py](https://gist.github.com/GramThanos/1a9f47267b447507cf4e8538d6340e2a) that gets cookies from all browsers and Hekadan's [decrypt_chrome_cookie.py](https://github.com/hekadan/decrypt-chrome-cookie/blob/master/decrypt_chrome_cookie.py) that gets cookies from Chrome.  

GramThanos's script worked for Edge and Firefox but didn't get data from Chrome.  
Hekadan's script works for Chrome.  

In true skiddie fashion, I put those two scripts together instead of fixing GramThanos's original script.

# Steps
```bash
# Install python3

# Save the script as "get_cookies.py"

# Install dependencies needed for this script
# "python.exe" might be "py" or "python3" depending on the environment
python.exe -m pip install pywin32
python.exe -m pip install pycryptodomex

# Run the script
# To display cookies
python.exe .\get_cookies.py
# To save cookies as a file in the same folder
python.exe .\get_cookies.py > all_the_cookies.csv
```

# Script
Save this and name it `get_cookies.py`.
```python
# Based on GimmeCookies v0.5 by GramThanos and decrypt_chrome_cookie.py by hekadan.

import os
import io
import re
import csv
import sys
import json
import base64
import sqlite3
import argparse
import configparser

# We only run on windows
if not os.name == 'nt':
	sys.exit('This script runs only in Windows')

# python.exe -m pip install pywin32
import win32crypt
# python.exe -m pip install pycryptodomex
from Cryptodome.Cipher import AES


# Match rules
rule_host = '.*'
rule_name = '.*'
rule_value = '.*'

# Output format
output_format = 'csv'

only_count = False

# Arguments
myArgParser = argparse.ArgumentParser(description='Gimme the cookies I want! Now!')
myArgParser.add_argument('-d', '--domain', metavar='domain', type=str, help='RegExp to match cookie domain')
myArgParser.add_argument('-n', '--name', metavar='name', type=str, help='RegExp to match cookie name')
myArgParser.add_argument('-v', '--value', metavar='value', type=str, help='RegExp to match cookie value')
myArgParser.add_argument('-f', '--format', metavar='format', type=str, help='Format to export data (CSV,JSON)')
myArgParser.add_argument('-c', '--count', action='store_true', help='Only count number of cookies found')
args = myArgParser.parse_args()

# Prepare rules
if args.domain:
	rule_host = args.domain
if args.name:
	rule_name = args.name
if args.value:
	rule_value = args.value

try:
	rule_host = re.compile(rule_host)
except Exception as e:
	rule_host = re.compile('.*')
	print('Invalid host name rule. Ignored.')

try:
	rule_name = re.compile(rule_name)
except Exception as e:
	rule_name = re.compile('.*')
	print('Invalid name rule. Ignored.')

try:
	rule_value = re.compile(rule_value)
except Exception as e:
	rule_value = re.compile('.*')
	print('Invalid value rule. Ignored.')


# Get selected format
if args.format:
	output_format = args.format.lower()

if args.count:
	only_count = True

# Chromium Load State
def base_chromium_loadStateInfo(path, state_file='Local State', cookies_file='Cookies'):
	# State path 
	state = os.path.join(path, state_file)
	# Check if state exist
	if not os.path.exists(state):
		print('State doesn\'t exist')
		return None

	# Read state content
	content = None
	try:
		with open(state, 'r') as file:
			content = json.loads(file.read())
	except:
		return None

	# Load encryption key
	encryption_key = None
	try:
		encryption_key = base64.b64decode(content['os_crypt']['encrypted_key'])
		encryption_key = encryption_key[5:]
		encryption_key = win32crypt.CryptUnprotectData(encryption_key, None, None, None, 0)[1]
	except:
		encryption_key = None
	

	# Load profiles
	profiles = []
	try:
		# For each profile
		for name in content['profile']['info_cache']:
			# Check if a cookies files exists
			if os.path.exists(os.path.join(path, name, cookies_file)):
				# Add profile on the list
				profiles.append(name)
	except:
		return None

	# Return data
	return {'profiles' : profiles, 'encryption_key': encryption_key}

# Chromium Get Cookies
def base_chromium_getCookies(path, encryption_key, cookies_file='Cookies'):
	# Generate paths
	path_cookies = os.path.join(path, cookies_file)

	# Check if cookies file exist
	if not os.path.exists(path_cookies):
		return []

	# Connect to the Database
	conn = sqlite3.connect(path_cookies)
	conn.text_factory = bytes
	cursor = conn.cursor()

	# Load cookies
	cookies = []
	# Get the cookies from database
	cursor.execute('SELECT host_key, name, value, encrypted_value FROM cookies')
	# For each cookie
	for host_key, name, value, encrypted_value in cursor.fetchall():
		# Decode host and name
		host_key = host_key.decode('utf-8')
		name = name.decode('utf-8')
		# Check filters
		if rule_host.match(host_key) and rule_name.match(name):
			value = None
			# Decrypt the encrypted_value
			try:
				# Try to decrypt as AES (2020 method)
				cipher = AES.new(encryption_key, AES.MODE_GCM, nonce=encrypted_value[3:3+12])
				value = cipher.decrypt_and_verify(encrypted_value[3+12:-16], encrypted_value[-16:])
			except:
				# If failed try with the old method
				value = win32crypt.CryptUnprotectData(encrypted_value, None, None, None, 0)[1] or value or 0
			value = value.decode('utf-8')
			# Check filters
			if rule_value.match(value):
				cookies.append({'host' : host_key, 'name': name, 'value': value})

	# Close database
	conn.close()

	# Return data
	return cookies

# Mozilla Load Profiles
def base_mozilla_loadProfilesInfo(path, profiles_file='profiles.ini', cookies_file='cookies.sqlite'):
	# Profiles ini path 
	profiles_ini = os.path.join(path, profiles_file)
	# Check if profiles ini exist
	if not os.path.exists(profiles_ini):
		return None

	# Read ini content
	config = configparser.ConfigParser()
	config.read(profiles_ini)

	# Load profiles
	profiles = []
	try:
		# For each profile
		for profile in config:
			if profile[:7] == 'Profile':
				# Check if a cookies files exists
				if os.path.exists(os.path.join(path, config[profile]['Path'], cookies_file)):
					# Add profile on the list
					profiles.append(config[profile]['Path'])
	except:
		return None

	# Return data
	return {'profiles' : profiles}

# Mozilla Get Cookies
def base_firefox_getCookies(path, cookies_file='cookies.sqlite'):
	# Generate paths
	path_cookies = os.path.join(path, cookies_file)

	# Check if file exist
	if not os.path.exists(path_cookies):
		return []

	# Connect to the Database
	conn = sqlite3.connect(path_cookies)
	cursor = conn.cursor()

	# Load cookies
	cookies = []
	# Get the cookies from database
	cursor.execute('SELECT host, name, value FROM moz_cookies')
	# For each cookie
	for host, name, value in cursor.fetchall():
		# Check filters
		if rule_host.match(host) and rule_name.match(name) and rule_value.match(value):
			cookies.append({'host' : host, 'name': name, 'value': value})

	# Close database
	conn.close()

	# Return data
	return cookies

# Get Cookies for Google Chrome
def browser_GoogleChrome(raw=False):
	# The path where the browser is installed "/../Local/Google/Chrome/User Data/"
	path = os.path.join(os.getenv("APPDATA"), '..', 'Local', 'Google', 'Chrome', 'User Data')
	# If installation folder was not found return
	if not os.path.exists(path):
		return [] if raw else None
	
	print(path)
	
	# Get info
	info = base_chromium_loadStateInfo(path)
	if not info:
		return [] if raw else None
	# Get cookies for each profile
	cookies = []
	for profile in info['profiles']:
		cookies += base_chromium_getCookies(os.path.join(path, profile), info['encryption_key'])
	# Return results
	return cookies if raw else {'browser' : "Google Chrome", 'cookies': cookies}
	
# Get Cookies for Microsoft Edge
def browser_MicrosoftEdge(raw=False):
	# The path where the browser is installed "/../Local/Microsoft/Edge/User Data/"
	path = os.path.join(os.getenv("APPDATA"), '..', 'Local', 'Microsoft', 'Edge', 'User Data')
	# If installation folder was not found return
	if not os.path.exists(path):
		return [] if raw else None
	# Get info
	info = base_chromium_loadStateInfo(path)
	if not info:
		return [] if raw else None
	# Get cookies for each profile
	cookies = []
	for profile in info['profiles']:
		cookies += base_chromium_getCookies(os.path.join(path, profile), info['encryption_key'])
	# Return results
	return cookies if raw else {'browser' : "Microsoft Edge", 'cookies': cookies}

# Get Cookies for Opera
def browser_Opera(raw=False):
	# The path where the browser is installed "/../Roaming/Opera Software/Opera Stable/"
	path = os.path.join(os.getenv("APPDATA"), '..', 'Roaming', 'Opera Software', 'Opera Stable')
	# If installation folder was not found return
	if not os.path.exists(path):
		return [] if raw else None
	# Get info
	info = base_chromium_loadStateInfo(path)
	if not info:
		return [] if raw else None
	# Get cookies for each profile
	cookies = []
	for profile in info['profiles']:
		cookies += base_chromium_getCookies(os.path.join(path, profile), info['encryption_key'])
	# Return results
	return cookies if raw else {'browser' : "Opera", 'cookies': cookies}

# Get Cookies for Mozilla Firefox
def browser_MozillaFirefox(raw=False):
	# The path where the browser is installed "/../Roaming/Mozilla/Firefox/"
	path = os.path.join(os.getenv("APPDATA"), '..', 'Roaming', 'Mozilla', 'Firefox')
	# If installation folder was not found return
	if not os.path.exists(path):
		return [] if raw else None
	# Get info
	info = base_mozilla_loadProfilesInfo(path)
	if not info:
		return [] if raw else None
	# Get cookies for each profile
	cookies = []
	for profile in info['profiles']:
		cookies += base_firefox_getCookies(os.path.join(path, profile))
	# Return results
	return cookies if raw else {'browser' : "Mozilla Firefox", 'cookies': cookies}

# Get Cookies for Mozilla Firefox
def browser_MozillaFirefoxDev(raw=False):
	# The path where the browser is installed "/../Roaming/Mozilla/Firefox/"
	path = os.path.join(os.getenv("APPDATA"), '..', 'Roaming', 'Mozilla', 'Firefox')
	# If installation folder was not found return
	if not os.path.exists(path):
		return [] if raw else None
	# Get info
	info = base_mozilla_loadProfilesInfo(path)
	if not info:
		return [] if raw else None
	# Get cookies for each profile
	cookies = []
	for profile in info['profiles']:
		cookies += base_firefox_getCookies(os.path.join(path, profile))
	# Return results
	return cookies if raw else {'browser' : "Mozilla Firefox Dev", 'cookies': cookies}


# Format output
def formatCookies(f, data, headers):
	print(data)
	if f == 'json':
		return json.dumps(data)
	if f == 'csv':
		output = io.StringIO()
		writer = csv.DictWriter(output, fieldnames=headers, delimiter=",", quoting=csv.QUOTE_MINIMAL)
		writer.writeheader()
		writer.writerows(data)
		return output.getvalue()
	return data

# Gather cookies
data = []
data.append(browser_GoogleChrome())
data.append(browser_MicrosoftEdge())
data.append(browser_MozillaFirefox())
data.append(browser_Opera())

# Remove None from list
data = [i for i in data if i]

# If counters were requested
if only_count:
	for i in range(len(data)):
		data[i]['cookies'] = len(data[i]['cookies'])
	print(formatCookies(output_format, data, ["browser", "cookies"]))

# If cookies were requested
else:
	cookies = []
	for browser in data:
		cookies += browser['cookies']
	print(formatCookies(output_format, cookies, ["host", "name", "value"]))
	
#------------Start of Hekadan's script-------------
#import os
#import json
#import base64
#import sqlite3
from shutil import copyfile
# Copy Cookies and Local State to current folder
copyfile(os.getenv("APPDATA") + "/../Local/Google/Chrome/User Data/Default/Network/Cookies", './Cookies')

# Load encryption key
encrypted_key = None
with open(os.getenv("APPDATA") + "/../Local/Google/Chrome/User Data/Local State", 'r') as file:
	encrypted_key = json.loads(file.read())['os_crypt']['encrypted_key']
encrypted_key = base64.b64decode(encrypted_key)
encrypted_key = encrypted_key[5:]
decrypted_key = win32crypt.CryptUnprotectData(encrypted_key, None, None, None, 0)[1]


# Connect to the Database
conn = sqlite3.connect('./Cookies')
conn.text_factory = bytes
cursor = conn.cursor()

# Get the results
cursor.execute('SELECT host_key, name, value, encrypted_value FROM cookies')
for host_key, name, value, encrypted_value in cursor.fetchall():
	# Decrypt the encrypted_value
	try:
		# Try to decrypt as AES (2020 method)
		cipher = AES.new(decrypted_key, AES.MODE_GCM, nonce=encrypted_value[3:3+12])
		decrypted_value = cipher.decrypt_and_verify(encrypted_value[3+12:-16], encrypted_value[-16:])
	except:
		# If failed try with the old method
		decrypted_value = win32crypt.CryptUnprotectData(encrypted_value, None, None, None, 0)[1].decode('utf-8') or value or 0
	# Todo: Add domain, host and value filters
	print(host_key.decode('utf-8') + ',' + name.decode('utf-8') + ',' + decrypted_value.decode('utf-8'))
	# Update the cookies with the decrypted value
	# This also makes all session cookies persistent
	cursor.execute('\
		UPDATE cookies SET value = ?, has_expires = 1, expires_utc = 99999999999999999, is_persistent = 1, is_secure = 0\
		WHERE host_key = ?\
		AND name = ?',
		(decrypted_value, host_key, name));

conn.commit()
conn.close()
```