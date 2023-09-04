The combination attack modes take in two wordlists as input and create combinations from them. This attack is useful because it is not uncommon for users to join two or more words together, thinking that this creates a stronger password, i.e., `welcomehome` or `hotelcalifornia`.

To demonstrate this attack, consider the following wordlists:

```shell-session
ChefBaki@htb[/htb]$ cat wordlist1

super
world
secret
```

```shell-session
ChefBaki@htb[/htb]$ cat wordlist2

hello
password
```

If given these two word lists `Hashcat` will produce exactly 3 x 2 = 6 words, such as the following:

```shell-session
ChefBaki@htb[/htb]$ awk '(NR==FNR) { a[NR]=$0 } (NR != FNR) { for (i in a) { print $0 a[i] } }' file2 file1

superhello
superpassword
worldhello
wordpassword
secrethello
secretpassword
```

This can also be done with `Hashcat` using the `--stdout` flag which can be very helpful for debugging purposes and seeing how the tool is handling things.

We can see what `Hashcat` will produce given the same two files in the following example:

```shell-session
ChefBaki@htb[/htb]$ hashcat -a 1 --stdout file1 file2
superhello
superpassword
worldhello
worldpassword
secrethello
secretpassword
```

#### Hashcat - Syntax

The syntax for the combination attack is:

```shell-session
ChefBaki@htb[/htb]$ hashcat -a 1 -m <hash type> <hash file> <wordlist1> <wordlist2>
```

This attack provides more flexibility and customization when using wordlists.

Let's see this example in practice. First, create the md5 of the password `secretpassword`.

```shell-session
ChefBaki@htb[/htb]$ echo -n 'secretpassword' | md5sum | cut -f1 -d' '  > combination_md5

2034f6e32958647fdff75d265b455ebf
```

Next, let's run `Hashcat` against the hash using the two wordlists above with the combination attack mode.

```shell-session
ChefBaki@htb[/htb]$ hashcat -a 1 -m 0 combination_md5 wordlist1 wordlist2

hashcat (v6.1.1) starting...
<SNIP>

Dictionary cache hit:
* Filename..: wordlist1
* Passwords.: 3
* Bytes.....: 19
* Keyspace..: 6

The wordlist or mask that you are using is too small.
This means that hashcat cannot use the full parallel power of your device(s).
Unless you supply more work, your cracking speed will drop.
For tips on supplying more work, see: https://hashcat.net/faq/morework

Approaching final keyspace - workload adjusted.  

2034f6e32958647fdff75d265b455ebf:secretpassword  
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: MD5
Hash.Target......: 2034f6e32958647fdff75d265b455ebf
Time.Started.....: Fri Aug 28 22:05:51 2020, (0 secs)
Time.Estimated...: Fri Aug 28 22:05:51 2020, (0 secs)
Guess.Base.......: File (wordlist1), Left Side
Guess.Mod........: File (wordlist2), Right Side
Speed.#1.........:       42 H/s (0.02ms) @ Accel:1024 Loops:2 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 6/6 (100.00%)
Rejected.........: 0/6 (0.00%)
Restore.Point....: 0/3 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-2 Iteration:0-2
Candidates.#1....: superhello -> secretpassword
```

Combination attacks are another powerful tool to keep in our arsenal. As demonstrated above, merely combining two words does not necessarily make a password stronger.

## Exercise - Supplementary word lists

#### Supplementary word list #1

```shell-session
sunshine
happy
frozen
golden
```

#### Supplementary word list #2

```shell-session
hello
joy
secret
apple
```