# Encryption

**Encryption**: a way to conceal information by altering it, so that it appears to be random data.  
**Decryption**: a way to convert encrypted data into information, usually using a key.  

_Quick glossary_  
|||
|---|---|
|**Plaintext**|The actual information|  
|**Key**|Data used to encrypt or decrypt plaintext|  
|**Cipher**|Algorithm used to perform encryption/decryption|  
|**Ciphertext**|The encrypted (seemingly random) data|

_The process_  
Plaintext ➜ *encryption using a key* ➜ Ciphertext ➜ *decryption using a key* ➜ Plaintext

**Why do we need encryption?**
- Privacy, security: Ensure only those with the right keys can read information
- Data-integrity: Ensure information isn't tampered with 
- Authentication: A way to prove identity _(see: HTTPS)_

## Types of encryption: Symmetric and Asymmetric
### Symmetric 
The same key is used for both encryption and decryption.  

Example:
Caesar Cipher is a method of encryption where each alphabet is shifted to the right by `n` characters.  
When the cipher is Caesar Cipher and "key" (`n`) is `2`:  
The data `abc` (Plaintext) becomes `cde` (Ciphertext) on encryption.     

Similarly, when we know that the key is `2`, we can easily decrypt it by shifting each alphabet to the left.  
Here, one key (`2`) is used for both encryption and decryption.  

[_A more complex version of the Caesar Cipher: Vigenère Cipher_](https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher)


#### Types of symmetric ciphers
**Block Ciphers:** Divides data into fixed-length blocks, encrypts it and chains them all into one long ciphertext.  
Padding is used to round off data to the nearest block size.  

_Notable ciphers of this type:_ Blowfish, AES(Advanced Encryption Standard), RC5(Rivest Cipher 5), DES(Data Encryption Standard)

**Stream ciphers:** Instead of dividing data into blocks, this encrypts plaintext data one digit at a time using the corresponding digit of the keystream (the key).  
The keystream is typically generated using a seed value.  
Faster than block ciphers and have lesser hardware requirements; typically used where length of data isn't known (like wireless connections).  

_Notable ciphers of this type:_ ChaCha, RC4, A5/1 (used in GSM), SOBER

#### Advantages
_Speed:_ Faster than asymmetric encryption

#### Disadvantages
_Key distribution:_ The other party needs the key to decrypt data. How do you securely transfer keys?  

### Asymmetric
**One key is used for encryption, and another key for decryption.**  
Similar to a physical lock and key - the function of a lock is to restrict access to something, the key unlocks the objects restricted by its lock.  
  
This opens up two possibilities not applicable to symmetric encryption:  
- **Encryption - We can receive data securely:**  
Publish the encryption key publicly, but keep the decryption key private.  
Anybody can encrypt data and send it to us; only we can decrypt it.  
Even if the encrypted data sent by someone is intercepted, it would be useless to them since only we have the decryption key.  
- **Authentication - We can send data with guaranteed integrity:**  
Publish the decryption key publicly, but keep the encryption key private.  
Add your name to the plaintext data, encrypt it and then publish the ciphertext.  
To verify that it's from you, the receiver can try decrypting the ciphertext using the public decryption key and see if it returns your name in plaintext.  
Nobody can impersonate you since nobody else has the encryption key that will generate ciphertext that matches the public decryption key. 

Generally, the key used for encryption is called the **public key**, and the key used for decryption is the **private key**.  

How do the public and private keys work?  
[A gentle introduction](https://blog.cloudflare.com/a-relatively-easy-to-understand-primer-on-elliptic-curve-cryptography/).

# Obfuscation
An operation to make data unintelligible, difficult to comprehend, and interpret; usually used in source code.  
**Objective:** Make it harder for other people to reverse-engineer your data (usually software).  

**Difference between encryption and obfuscation:**  
If encryption is encrypting data and writing the ciphertext on a piece of paper, obfuscation is writing the plaintext with a bad handwriting.  

Obfuscation adds to the effort needed to reverse-engineer components and compliments security, but is **not** a replacement for security.

**Examples:**  
- Modifying file and folder names
- Running your programs on non-standard ports
- Renaming variables to garbage values to make code harder to understand

See also: [Security through obscurity](https://securitytrails.com/blog/security-through-obscurity)  
Try it online - [Online JS obfuscator](https://www.obfuscator.io/)

# Encoding
Encoding is the process of converting data into a format required for information processing needs.  
This is similar to converting data from one known language to another.  

Encoding:
- **Does not seek to offer security** or encrypt data
- Is **easily reversible**
- Used to facilitate data transfer between systems

In some languages, issues might arise when transferring data with symbols like `;<>"'`.  
In those cases, data can be encoded before transferring it, which can then be decoded when used.   

**Example:**  
*Note that encoded text is similar - this does not help "hide" data, and is not comparable to encryption in any way.*
|Plaintext|Base64 encoded|
|:---:|:---:|
|test;|`dGVzdDs=`|
|text;|`dGV4dDs=`|
|text!|`dGV4dCE=`|

Try it online - [Online Base64 Encoder](https://www.base64encode.org/)

# Hashing
A method that converts text of any size into another fixed-length, practically unique text. This resulting text is called the **message digest** or **hash**.  
Hashing algorithms should be:  
- **A one-way cryptographic function:** Finding original plaintext data from a given hash should be practically impossible. 
- **Consistent and repeatable:** When the same algorithm is used, the same plaintext data always yields the same hash. 
- **Unique:** The probability of multiple distinct inputs yielding the same hash should be very less.

Even if a single character in the input plaintext differs, the resulting hash varies by a large margin.  
If two distinct inputs yield the same hash value, it's called a **collision**.  

**Difference between encryption and obfuscation:** In encryption, we can get the original plaintext from excrypted text if key and algorithm are known. In hashing, we cannot get the original plaintext from a hash even if algorithm is known.    

**Popular cryptographic hash functions:** SHA-512, NTLMv2 (NT LAN Manager)  
Not recommended since weaknesses have been found: MD5 (Message Digest 5) and SHA1 (Secure Hashing Algorithm) 

**Example:**  
*Note that even with a single character change, results differ completely*
|Plaintext|MD5 hash|SHA-1 hash|
|:---:|:---:|:---:|
|test|`098f6bcd4621d373cade4e832627b4f6`|`a94a8fe5ccb19ba61c4c0873d391e987982fbbd3`|
|text|`1cb251ec0d568de6a929b520c4aed8d1`|`372ea08cab33e71c02c651dbc83a474d32c676ea`|
|t|`e358efa489f58062f10dd7316b65649e`|`8efd86fb78a56a5145ed7739dcb00c78581c5375`|

Try it online - [Online hash generator](https://dencode.com/hash)  

## Applications
- Used to verify the **integrity** of data.  
Any file or document can be hashed; to ensure that a file has not been tampered with, we can compute the hash of the original and the file we have and check that it matches. Even if a single change is made to the file, the hash would be different.   
- Password storage.

# How do they all fit together? A case-study - the User Login process.
Most applications have login mechanisms - getting credentials (username and password) from the user to authenticate them.  
- **Encoding:** When the username and password are sent from the client to the server, the data is **encoded** so that special characters don't cause any issues during transfer.  
- **Encryption:** In order to provide encryption in transit and prevent eavesdroppers from getting the data during transit, HTTPS is used, which **encrypts** the data to send it securely.
- **Hashing:** Once it reaches the server, the password is **hashed** and then stored in the DB.  
This ensures that the plaintext password isn't stored anywhere in the DB, ensuring that even if the DB is breached, plaintext user passwords are not compromised.  
- **Obfuscation:** All these operations are performed by software; to deter malicious actors and to make this exact flow harder to decipher, the software's code is **obfuscated**.

# References
[The SSL Store - Symmetric vs Asymmetric encryption](https://www.thesslstore.com/blog/difference-asymmetric-encryption-algorithms-vs-symmetric-encryption-algorithms/)  
[Phemex - What is symmetric encryption?](https://phemex.com/academy/what-is-symmetric-key-encryption)  
[ctf101 - What are block ciphers?](https://ctf101.org/cryptography/what-are-block-ciphers/)  
[Wiki - Obfuscation](https://en.wikipedia.org/wiki/Obfuscation_(software))  
[FreeCodeCamp - What is Hashing?](https://www.freecodecamp.org/news/what-is-hashing/)  