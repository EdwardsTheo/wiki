Mask attacks are used to generate words matching a specific pattern. This type of attack is particularly useful when the password length or format is known. A mask can be created using static characters, ranges of characters (e.g. [a-z] or [A-Z0-9]), or placeholders. The following list shows some important placeholders:

![Pasted-image-20230113091325.png](/Images/Pasted-image-20230113091325.png)

The above placeholders can be combined with options "`-1`" to "`-4`" which can be used for custom placeholders. See the _Custom charsets_ section [here](https://hashcat.net/wiki/doku.php?id=mask_attack) for a detailed breakdown of each of these four command-line parameters that can be used to configure four custom charsets.

Consider the company Inlane Freight, which this time has passwords with the scheme "`ILFREIGHT<userid><year>`," where userid is 5 characters long. The mask "`ILFREIGHT?l?l?l?l?l20[0-1]?d`" can be used to crack passwords with the specified pattern, where "`?l`" is a letter and "`20[0-1]?d`" will include all years from 2000 to 2019.

Let's try creating a hash and cracking it using this mask.

#### Creating MD5 hashes

```shell-session
ChefBaki@htb[/htb]$ echo -n 'ILFREIGHTabcxy2015' | md5sum | tr -d " -" > md5_mask_example_hash
```

In the below example, the attack mode is `3`, and the hash type for MD5 is `0`.

#### Hashcat - Mask Attack

```shell-session
ChefBaki@htb[/htb]$ hashcat -a 3 -m 0 md5_mask_example_hash -1 01 'ILFREIGHT?l?l?l?l?l20?1?d'

hashcat (v6.1.1) starting...
<SNIP>

d53ec4d0b37bbf565b1e09d64834e1ae:ILFREIGHTabcxy2015
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: MD5
Hash.Target......: d53ec4d0b37bbf565b1e09d64834e1ae
Time.Started.....: Fri Aug 28 22:08:44 2020, (43 secs)
Time.Estimated...: Fri Aug 28 22:09:27 2020, (0 secs)
Guess.Mask.......: ILFREIGHT?l?l?l?l?l20?1?d [18]
Guess.Charset....: -1 01, -2 Undefined, -3 Undefined, -4 Undefined 
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  3756.3 kH/s (0.36ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 155222016/237627520 (65.32%)
Rejected.........: 0/155222016 (0.00%)
Restore.Point....: 155215872/237627520 (65.32%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: ILFREIGHTuisba2015 -> ILFREIGHTkmrff2015
```

The "`-1`" option was used to specify a placeholder with just 0 and 1. `Hashcat` could crack the hash in 43 seconds on CPU power. The "`--increment`" flag can be used to increment the mask length automatically, with a length limit that can be supplied using the "`--increment-max`" flag.