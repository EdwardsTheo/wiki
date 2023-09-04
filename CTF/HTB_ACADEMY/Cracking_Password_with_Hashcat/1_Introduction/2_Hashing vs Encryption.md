## Hashing

Hashing is the process of converting some text to a string, which is unique to that particular text. Usually, a hash function always returns hashes with the same length irrespective of the type, length, or size of the data. Hashing is a one-way process, meaning there is no way of reconstructing the original plaintext from a hash. Hashing can be used for various purposes; for example, the [MD5](https://en.wikipedia.org/wiki/MD5) and [SHA256](https://en.wikipedia.org/wiki/SHA-2) algorithms are usually used to verify file integrity, while algorithms such as [PBKDF2](https://en.wikipedia.org/wiki/PBKDF2) are used to hash passwords before storage. Some hash functions can be keyed (i.e., an additional secret is used to create the hash). One such example is [HMAC](https://en.wikipedia.org/wiki/HMAC), which acts as a [checksum](https://en.wikipedia.org/wiki/Checksum) to verify if a particular message was tampered with during transmission.

As hashing is a one-way process, the only way to attack it is to use a list containing possible passwords. Each password from this list is hashed and compared to the original hash.

One protection employed against the brute-forcing of hashes is "salting." A salt is a random piece of data added to the plaintext before hashing it. This increases the computation time but does not prevent brute force altogether.

Let's consider the plaintext password value "p@ssw0rd". The MD5 hash for this can be calculated as follows:

```shell-session
ChefBaki@htb[/htb]$ echo -n "p@ssw0rd" | md5sum

0f359740bd1cda994f8b55330c86d845
```

Now, suppose a random salt such as "123456" is introduced and appended to the plaintext.

```shell-session
ChefBaki@htb[/htb]$ echo -n "p@ssw0rd123456" | md5sum

f64c413ca36f5cfe643ddbec4f7d92d0
```

A completely new hash was generated using this method, which will not be present in any pre-computed list. An attacker trying to crack this hash will have to sacrifice extra time to append this salt before calculating the hash.

Some hash functions such as MD5 have also been vulnerable to collisions, where two sets of plaintext can produce the same hash.

## Encryption

Encryption is the process of converting data into a format in which the original content is not accessible. Unlike hashing, encryption is reversible, i.e., it's possible to decrypt the ciphertext (encrypted data) and obtain the original content. Some classic examples of encryption ciphers are the [Caesar cipher](https://en.wikipedia.org/wiki/Caesar_cipher), [Bacon's cipher](https://en.wikipedia.org/wiki/Bacon%27s_cipher) and [Substitution cipher](https://en.wikipedia.org/wiki/Substitution_cipher). Encryption algorithms are of two types: Symmetric and Asymmetric.

## Symmetric Encryption

Symmetric algorithms use a key or secret to encrypt the data and use the same key to decrypt it. A basic example of symmetric encryption is XOR.

```shell-session
ChefBaki@htb[/htb]$ python3

Python 3.8.3 (default, May 14 2020, 11:03:12) 
[GCC 9.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from pwn import xor
>>> xor("p@ssw0rd", "secret")
b'\x03%\x10\x01\x12D\x01\x01'
```

In the image above, the plaintext is `p@ssw0rd,` and the key is `secret`. Anyone who has the key can decrypt the ciphertext and obtain the plaintext.

```shell-session
ChefBaki@htb[/htb]$ python3

Python 3.8.3 (default, May 14 2020, 11:03:12) 
[GCC 9.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from pwn import xor
>>> xor('\x03%\x10\x01\x12D\x01\x01', "secret")
b'p@ssw0rd'
```

The `b` in the above outputs denotes a byte string. This distinction was not made pre `Python3`.

Some other examples of symmetric algorithms are [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), [DES](https://en.wikipedia.org/wiki/Data_Encryption_Standard), [3DES](https://en.wikipedia.org/wiki/Triple_DES) and [Blowfish](https://en.wikipedia.org/wiki/Blowfish_(cipher)#The_algorithm). These algorithms can be vulnerable to attacks such as key bruteforcing, [frequency analysis](https://en.wikipedia.org/wiki/Frequency_analysis), [padding oracle attack](https://en.wikipedia.org/wiki/Padding_oracle_attack), etc.

## Asymmetric Encryption

On the other hand, asymmetric algorithms divide the key into two parts (i.e., public and private). The public key can be given to anyone who wishes to encrypt some information and pass it securely to the owner. The owner then uses their private key to decrypt the content. Some examples of asymmetric algorithms are [RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)), [ECDSA](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm), and [Diffie-Hellman](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange).

One of the prominent uses of asymmetric encryption is the `Hypertext Transfer Protocol Secure` (`HTTPS`) protocol in the form of `Secure Sockets Layer` (`SSL`). When a client connects to a server hosting an `HTTPS` website, a public key exchange occurs. The client's browser uses this public key to encrypt any kind of data sent to the server. The server decrypts the incoming traffic before passing it on to the processing service.