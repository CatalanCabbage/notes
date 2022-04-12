# Bash commands
```bash
# Download a file
wget <url> 

# View contents
cat <file_path> #Shows complete contents
nl <file_path> #Shows contents with line numbers
less <file_path> #Shows contents a page at a time
head <file_path> #Shows first 10 lines
tail <file_path> #Shows last 10 lines

# Make a file executable
chmod +x <file_path>

# Move files
mv <src_path> <destination_path> #Can be used to rename files too

# Copy files
cp <src_path> <destination_path>

# View image metadata
exiftool <file_path>
identify <file_path> #ImageMagick

# Split
cat file.txt | cut -c 10 #Show first 10 characters of each line
cat file.txt | cut -d ',' -f 2 #Split by delimiter ',' and show the 2nd field in each line
cat file.txt | cut -d ',' -f 1-2 #Split by delimiter ',' and show fields 1 and 2 in each line

# Search
cat file.txt | grep "keywordRegex"
cat file.txt | grep -i "keywordRegex"   #Case insensitive
cat file.txt | grep "keywordRegex" -A 2  #For each match, show next 2 lines too
grep -HRi "data" *  #Search through all files (H) recursively (R)
```