Most hashing algorithms produce hashes of a constant length. The length of a particular hash can be used to map it to the algorithm it was hashed with. For example, a hash of 32 characters in length can be an MD5 or NTLM hash.

Sometimes, hashes are stored in certain formats. For example, `hash:salt` or `$id$salt$hash`.

The hash `2fc5a684737ce1bf7b3b239df432416e0dd07357:2014` is a SHA1 hash with the salt of `2014`.

The hash `$6$vb1tLY1qiY$M.1ZCqKtJBxBtZm1gRi8Bbkn39KU0YJW1cuMFzTRANcNKFKR4RmAQVk4rqQQCkaJT6wXqjUkFcA/qNxLyqW.U/` contains three fields delimited by `$`, where the first field is the `id`, i.e., `6`. This is used to identify the type of algorithm used for hashing. The following list contains some ids and their corresponding algorithms.

```shell-session
$1$  : MD5
$2a$ : Blowfish
$2y$ : Blowfish, with correct handling of 8 bit characters
$5$  : SHA256
$6$  : SHA512
```

The next field, `vb1tLY1qiY`, is the salt used during hashing, and the final field is the actual hash.

Open and closed source software use many different kinds of hash formats. For example, the `Apache` web server stores its hashes in the format `$apr1$71850310$gh9m4xcAn3MGxogwX/ztb.`, while `WordPress` stores hashes in the form `$P$984478476IagS59wHZvyQMArzfx58u.`.

## Hashid

[Hashid](https://github.com/psypanda/hashID) is a `Python` tool, which can be used to detect various kinds of hashes. At the time of writing, `hashid` can be used to identify over 200 unique hash types, and for others, it will make a best-effort guess, which will still require some additional work to narrow it down. The full list of supported hashes can be found [here](https://github.com/psypanda/hashID/blob/master/doc/HASHINFO.xlsx). It can be installed using `pip`.

```shell-session
ChefBaki@htb[/htb]$ pip install hashid
```

Hashes can be supplied as command-line arguments or using a file.

```shell-session
ChefBaki@htb[/htb]$ hashid '$apr1$71850310$gh9m4xcAn3MGxogwX/ztb.'

Analyzing '$apr1$71850310$gh9m4xcAn3MGxogwX/ztb.'
[+] MD5(APR) 
[+] Apache MD5
```

```shell-session
ChefBaki@htb[/htb]$ hashid hashes.txt 

--File 'hashes.txt'--
Analyzing '2fc5a684737ce1bf7b3b239df432416e0dd07357:2014'
[+] SHA-1 
[+] Double SHA-1 
[+] RIPEMD-160 
[+] Haval-160 
[+] Tiger-160 
[+] HAS-160 
[+] LinkedIn 
[+] Skein-256(160) 
[+] Skein-512(160) 
[+] Redmine Project Management Web App 
[+] SMF ≥ v1.1 
Analyzing '$P$984478476IagS59wHZvyQMArzfx58u.'
[+] Wordpress ≥ v2.6.2 
[+] Joomla ≥ v2.5.18 
[+] PHPass' Portable Hash 
--End of file 'hashes.txt'--
```

If known, `hashid` can also provide the corresponding `Hashcat` hash mode with the `-m` flag if it is able to determine the hash type.

```shell-session
ChefBaki@htb[/htb]$ hashid '$DCC2$10240#tom#e4e938d12fe5974dc42a90120bd9c90f' -m
Analyzing '$DCC2$10240#tom#e4e938d12fe5974dc42a90120bd9c90f'
[+] Domain Cached Credentials 2 [Hashcat Mode: 2100
```

## Context is Important

It is not always possible to identify the algorithm based on the obtained hash. Depending on the software, the plaintext might undergo multiple encryption rounds and salting transformations, making it harder to recover.

It is important to note that `hashid` uses regex to make a best-effort determination for the type of hash provided. Oftentimes `hashid` will provide many possibilities for a given hash, and we will still be left with a certain amount of guesswork to identify a given hash. This may happen during a CTF, but we usually have some context around the type of hash we are looking to identify during a penetration test. Was it obtained via an Active Directory attack or from a Windows host? Was it obtained through the successful exploitation of a SQL injection vulnerability? Knowing where a hash came from will greatly help us narrow down the hash type and, therefore, the `Hashcat` hash mode necessary to attempt to crack it. `Hashcat` provides an excellent [reference](https://hashcat.net/wiki/doku.php?id=example_hashes), which maps hash modes to example hashes. This reference is invaluable during a penetration test to determine the type of hash we are dealing with and the associated hash mode required to pass it to `Hashcat`.

For example, passing the hash `a2d1f7b7a1862d0d4a52644e72d59df5:500:lp@trash-mail.com` to `hashid` will give us various possibilities:

```shell-session
ChefBaki@htb[/htb]$ hashid 'a2d1f7b7a1862d0d4a52644e72d59df5:500:lp@trash-mail.com'

Analyzing 'a2d1f7b7a1862d0d4a52644e72d59df5:500:lp@trash-mail.com'
[+] MD5 
[+] MD4 
[+] Double MD5 
[+] LM 
[+] RIPEMD-128 
[+] Haval-128 
[+] Tiger-128 
[+] Skein-256(128) 
[+] Skein-512(128) 
[+] Lotus Notes/Domino 5 
[+] Skype 
[+] Lastpass 
```

However, a quick look through the `Hashcat` example hashes reference will help us determine that it is indeed a Lastpass hash, which is hash mode `6800`. Context is important during assessments and is important to consider when working with any tool that attempts to identify hash types.