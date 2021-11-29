> WIP

# Encryption
Encryption: a way to conceal information by altering it, so that it appears to be random data.  
Decryption: a way to convert encrypted data into information, usually using a key.  

**Plaintext**: The actual information  
**Key**: Data used to encrypt or decrypt plaintext  
**Cipher**: Algorithm used to perform encryption/decryption  
**Ciphertext**: The encrypted (seemingly random) data  

Plaintext *>encryption using a key>* Ciphertext *>decryption using a key>* Plaintext

Why do we need encryption?
- Privacy, security: Ensure only those with the right keys can read information
- Data-integrity: Ensure information isn't tampered with 
- Authentication: A way to prove identity _(see: HTTPS)_

## Types of encryption: Symmetric and Asymmetric
### Symmetric 
The same key is used for both encryption and decryption.  

Example:
Caesar Cipher is a method of encryption where each alphabet is shifted to the right by `n` characters.  
When the cipher is Caesar Cipher, "key" (`n`) is `2`:  
The data `abcde` (Plaintext) becomes `cdefg` (Ciphertext) on encryption.     

Similarly, when we know that the key is `2`, we can easily decrypt it by shifting each alphabet to the left.  
Here, one key (`2`) is used for both encryption and decryption.  

[_A more complex version of the Caesar Cipher: Vigenère Cipher_](https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher)


#### Types of symmetric ciphers
**Block Ciphers:** Divides data into fixed-length blocks, encrypts it and chains them all into one long ciphertext.  
Padding is used to round off data to the nearest block size.  
Notable ciphers of this type: Blowfish, AES(Advanced Encryption Standard), RC5(Rivest Cipher 5), DES(Data Encryption Standard)

**Stream ciphers:** Instead of dividing data into blocks, this encrypts plaintext data one digit at a time using the corresponding digit of the keystream (the key).  
The keystream is typically generated using a seed value.  
Faster than block ciphers and have lesser hardware requirements; typically used where length of data isn't known (like wireless connections).  

Notable ciphers of this type: ChaCha, RC4, A5/1 (used in GSM), SOBER

#### Advantages
Speed: Faster than asymmetric encryption

#### Disadvantages
Key distribution: How do you securely transfer keys to the other party?  

### Asymmetric
One key is used for encryption, and another key for decryption.  
Similar to a physical lock and key - the function of a lock is to restrict access to something, the key unlocks the objects restricted by its lock.  
  
This opens up two possibilities not applicable to symmetric encryption:  
- **Encryption: We can receive data securely:**  
Publish the encryption key publicly, but keep the decryption key private.  
The sender can encrypt data and then send it to us; only we can decrypt it. In this case, even if the encrypted data is intercepted, it would be useless (since only we have the decryption key).  
- **Authentication: We can send data with guaranteed integrity:**  
Publish the decryption key publicly, but keep the encryption key private.  
Add your name to the plaintext data, encrypt it and then publish the ciphertext. To verify that it's from you, the receiver can try decrypting the ciphertext using the public decryption key and see if it returns your name in plaintext.   

Generally, the key used for encryption is called the **public key**, and the key used for decryption is the **private key**.  


[The SSL Store - Symmetric vs Asymmetric encryption](https://www.thesslstore.com/blog/difference-asymmetric-encryption-algorithms-vs-symmetric-encryption-algorithms/)
[Phemex - What is symmetric encryption?](https://phemex.com/academy/what-is-symmetric-key-encryption)
[ctf101 - What are block ciphers?](https://ctf101.org/cryptography/what-are-block-ciphers/)