---

Hashcat has 5 different attack modes that have different applications depending on the type of hash you are trying to crack and the complexity of the password. The most straightforward but extremely effective attack type is the dictionary attack. It is not uncommon to encounter organizations with weak password policies whose users select common words and phrases with little to no complexity as their passwords. Based on an analysis of millions of leaked passwords, the organization SplashData listed the following as the top 5 most common passwords of 2020:

#### Password List - Top 5 (2020)

```shell-session
123456
123456789
qwerty
password
1234567
```

Despite training users on security awareness, users will often choose one out of convenience if an organization allows weak passwords.

These passwords would appear in most any dictionary file used to perform this type of attack. There are many sources for obtaining password lists such as [SecLists](https://github.com/danielmiessler/SecLists/tree/master/Passwords), a large collection of password lists, and the `rockyou.txt` wordlist, which is found in most penetration testing Linux distros.

We can also find large wordlists such as [CrackStation's Password Cracking Dictionary](https://crackstation.net/crackstation-wordlist-password-cracking-dictionary.htm), which contains 1,493,677,782 words and is 15GB in size. Depending on needs and computing requirements, there are much larger wordlists made up of cleartext passwords obtained from multiple breaches and password dumps, some equaling over 40GB in size. These can be extremely useful when attempting to crack a single password, critical to your engagement's success, on a powerful GPU or when performing a domain password analysis of all of the user passwords in an Active Directory environment by attempting to crack as many of the NTLM password hashes in the NTDS.dit file as possible.

---

## Straight or Dictionary Attack

As the name suggests, this attack reads from a wordlist and tries to crack the supplied hashes. Dictionary attacks are useful if you know that the target organization uses weak passwords or just wants to run through some cracking attempts rather quickly. This attack is typically faster to complete than the more complex attacks discussed later in this module. It's basic syntax is:

#### Hashcat - Syntax

```shell-session
ChefBaki@htb[/htb]$ hashcat -a 0 -m <hash type> <hash file> <wordlist>
```

For example, the following commands will crack a SHA256 hash using the `rockyou.txt` wordlist.

```shell-session
ChefBaki@htb[/htb]$ echo -n '!academy' | sha256sum | cut -f1 -d' ' > sha256_hash_example
ChefBaki@htb[/htb]$ hashcat -a 0 -m 1400 sha256_hash_example /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

Dictionary cache built:
* Filename..: /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 2 secs

Approaching final keyspace - workload adjusted.  

006fc3a9613f3edd9f97f8e8a8eff3b899a2d89e1aabf33d7cc04fe0728b0fe6:!academy
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: SHA2-256
Hash.Target......: 006fc3a9613f3edd9f97f8e8a8eff3b899a2d89e1aabf33d7cc...8b0fe6
Time.Started.....: Fri Aug 28 21:58:44 2020 (4 secs)
Time.Estimated...: Fri Aug 28 21:58:48 2020 (0 secs)
Guess.Base.......: File (/opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  3383.5 kH/s (0.46ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 14344385/14344385 (100.00%)
Rejected.........: 0/14344385 (0.00%)
Restore.Point....: 14340096/14344385 (99.97%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: $HEX[216361726f6c796e] -> $HEX[042a0337c2a156616d6f732103]

Started: Fri Aug 28 21:58:05 2020
Stopped: Fri Aug 28 21:58:49 2020
```

In the above example, the hash cracked in 4 seconds. Cracking speed varies depending on the underlying hardware, hash type, and complexity of the password.

Let's look at a more complex hash such as [Bcrypt](https://en.wikipedia.org/wiki/Bcrypt), which is a type of password hash based on the [Blowfish](https://en.wikipedia.org/wiki/Blowfish_(cipher)) cipher. It utilizes a salt to protect it from rainbow table attacks and can have many rounds of the algorithm applied, making the hash resistant to brute force attacks even with a large password cracking rig.

For example, take the bcrypt hash of the same password "`!academy`" which would be `$2a$05$ZdEkj8cup/JycBRn2CX.B.nIceCYR8GbPbCCg6RlD7uvuREexEbVy` with 5 rounds of the Blowfish algorithm applied. This hash run on the same hardware with the same wordlist takes considerably longer to crack.

At any time during the cracking process, you can hit the "`s`" key to get a status on the cracking job, which shows that to attempt every password in the `rockyou.txt` wordlist will take over 1.5 hours. Applying more rounds of the algorithm will increase cracking time exponentially. In the case of hashes such as bcrypt, it is often better to use smaller, more targeted, wordlists.

#### Hashcat - Status

```shell-session
[s]tatus [p]ause [b]ypass [c]heckpoint [q]uit => s

Session..........: hashcat
Status...........: Running
Hash.Name........: bcrypt $2*$, Blowfish (Unix)
Hash.Target......: $2a$05$ZdEkj8cup/JycBRn2CX.B.nIceCYR8GbPbCCg6RlD7uv...exEbVy
Time.Started.....: Mon Jun 22 19:43:40 2020 (3 mins, 10 secs)
Time.Estimated...: Mon Jun 22 21:20:28 2020 (1 hour, 33 mins)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:     2470 H/s (8.98ms) @ Accel:8 Loops:16 Thr:1 Vec:8
Recovered........: 0/1 (0.00%) Digests
Progress.........: 468576/14344385 (3.27%)
Rejected.........: 0/468576 (0.00%)
Restore.Point....: 468576/14344385 (3.27%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:16-32
Candidates.#1....: septiembre29 -> sep1101
```

As we have seen in this section, dictionary attacks can be very effective for weak passwords, but the attack's efficacy also depends on the type of hash targeted. Certain types of weaker passwords can be much more difficult to crack just based on the hashing algorithm in use. This does not mean that a weak password using a stronger hashing algorithm is any more "secure." Password cracking hardware varies, and "cracking rigs" with many GPUs could make short work of a password hash that would take hours or days on a single CPU.