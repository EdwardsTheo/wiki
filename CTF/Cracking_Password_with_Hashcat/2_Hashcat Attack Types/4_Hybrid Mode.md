Hybrid mode is a variation of the combinator attack, wherein multiple modes can be used together for a fine-tuned wordlist creation. This mode can be used to perform very targeted attacks by creating very customized wordlists. It is particularly useful when you know or have a general idea of the organization's password policy or common password syntax. The attack mode for the hybrid attack is "`6`".

Let's consider a password such as "`football1$`". The example below shows how a wordlist can be used in combination with a mask.

#### Creating Hybrid Hash

```shell-session
ChefBaki@htb[/htb]$ echo -n 'football1$' | md5sum | tr -d " -" > hybrid_hash
```

Hashcat reads words from the wordlist and appends a unique string based on the mask supplied. In this case, the mask "`?d?s`" tells hashcat to append a digit and a special character at the end of each word in the `rockyou.txt` wordlist.

#### Hashcat - Hybrid Attack using Wordlists

```shell-session
ChefBaki@htb[/htb]$ hashcat -a 6 -m 0 hybrid_hash /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt '?d?s'

hashcat (v6.1.1) starting...
<SNIP>

f7a4a94ff3a722bf500d60805e16b604:football1$      
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: MD5
Hash.Target......: f7a4a94ff3a722bf500d60805e16b604
Time.Started.....: Fri Aug 28 22:11:15 2020, (0 secs)
Time.Estimated...: Fri Aug 28 22:11:15 2020, (0 secs)
Guess.Base.......: File (/opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt), Left Side
Guess.Mod........: Mask (?d?s) [2], Right Side
Guess.Queue.Base.: 1/1 (100.00%)
Guess.Queue.Mod..: 1/1 (100.00%)
Speed.#1.........:  5118.2 kH/s (11.56ms) @ Accel:768 Loops:82 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 755712/4733647050 (0.02%)
Rejected.........: 0/755712 (0.00%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:82-164 Iteration:0-82
Candidates.#1....: 1234562= -> class083~
```

Attack mode "`7`" can be used to prepend characters to words using a given mask. The following example shows a mask using a custom character set to add a prefix to each word in the `rockyou.txt` wordlist. The custom character mask "`20?1?d`" with the custom character set "`-1 01`" will prepend various years to each word in the wordlist (i.e., 2010, 2011, 2012..).

#### Creating another Hybrid Hash

```shell-session
ChefBaki@htb[/htb]$ echo -n '2015football' | md5sum | tr -d " -" > hybrid_hash_prefix 
```

#### Hashcat - Hybrid Attack using Wordlists with Masks

```shell-session
ChefBaki@htb[/htb]$ hashcat -a 7 -m 0 hybrid_hash_prefix -1 01 '20?1?d' /opt/useful/SecLists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...
<SNIP> 

eac4fe196339e1b511278911cb77d453:2015football    
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: MD5
Hash.Target......: eac4fe196339e1b511278911cb77d453
Time.Started.....: Thu Nov 12 01:32:34 2020 (0 secs)
Time.Estimated...: Thu Nov 12 01:32:34 2020 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt), Right Side
Guess.Mod........: Mask (20?1?d) [4], Left Side
Guess.Charset....: -1 01, -2 Undefined, -3 Undefined, -4 Undefined
Speed.#1.........:     8420 H/s (0.22ms) @ Accel:384 Loops:64 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 1280/286887700 (0.00%)
Rejected.........: 0/1280 (0.00%)
Restore.Point....: 0/20 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-64 Iteration:0-64
Candidates.#1....: 2001123456 -> 2017charlie
```





