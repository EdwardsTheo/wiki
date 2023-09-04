## Hashcat

[Hashcat](https://hashcat.net/hashcat/) is a popular open-source password cracking tool.

`Hashcat` can be downloaded from the [website](https://hashcat.net/hashcat/) using `wget` and then decompressed using the `7z` (7-Zip file archiver) via the command line. The full help menu can be viewed by typing `hashcat -h`. The latest version of `Hashcat` at the time of writing is version 6.1.1. Version 6.0.0 was a major release that introduced several enhancements over version 5.x. Some of the changes include performance improvements and 51 new algorithms (or supported hash types, also known as hash modes) for a total of over 320 supported algorithms at the time of writing. We can also download a standalone binary of the latest release for Windows and Unix/Linux systems from Hashcat's website or compile from the source.

The `Hashcat` team does not maintain any packages, aside from the official GitHub [repo](https://github.com/hashcat/hashcat). Any other repos (i.e., installing from `apt`), are left up to the 3rd party to keep up-to-date. The latest version can always be obtained directly from their GitHub repo and install from the source. For our purposes, we will demonstrate installation within the Pwnbox, which, at the time of writing, is using the latest version (6.1.1).

#### Hashcat Installation

```shell-session
ChefBaki@htb[/htb]$ sudo apt install hashcat
ChefBaki@htb[/htb]$ hashcat -h

hashcat (v6.1.1) starting...

Usage: hashcat [options]... hash|hashfile|hccapxfile [dictionary|mask|directory]...

- [ Options ] -

 Options Short / Long           | Type | Description                                          | Example
================================+======+======================================================+=======================
 -m, --hash-type                | Num  | Hash-type, see references below                      | -m 1000
 -a, --attack-mode              | Num  | Attack-mode, see references below                    | -a 3
 -V, --version                  |      | Print version                                        |
 -h, --help                     |      | Print help                                           |
     --quiet                    |      | Suppress output                                      |
     --hex-charset              |      | Assume charset is given in hex                       |
     --hex-salt                 |      | Assume salt is given in hex                          |
     --hex-wordlist             |      | Assume words in wordlist are given in hex            |
     --force                    |      | Ignore warnings                                      |
     --status                   |      | Enable automatic update of the status screen         |
     --status-json              |      | Enable JSON format for status output                 |
     --status-timer             | Num  | Sets seconds between status screen updates to X      | --status-timer=1
     --stdin-timeout-abort      | Num  | Abort if there is no input from stdin for X seconds  | --stdin-timeout-abort=300
     --machine-readable         |      | Display the status view in a machine-readable format |

<SNIP>
```

The folder contains 64-bit binaries for both Windows and Linux. The `-a` and `-m` arguments are used to specify the type of attack mode and hash type. `Hashcat` supports the following attack modes:

![Pasted-image-20230112205642.png](/Images/Pasted-image-20230112205642.png)

The hash type value is based on the algorithm of the hash to be cracked. A complete list of hash types and their corresponding examples can be found [here](https://hashcat.net/wiki/doku.php?id=example_hashes). The table helps in quickly identifying the number for a given hash type. You can also view the list of example hashes via the command line using the following command:

#### Hashcat - Example Hashes

```shell-session
ChefBaki@htb[/htb]$ hashcat --example-hashes | less

hashcat (v6.1.1) starting...

MODE: 0
TYPE: MD5
HASH: 8743b52063cd84097a65d1633f5c74f5
PASS: hashcat

MODE: 10
TYPE: md5($pass.$salt)
HASH: 3d83c8e717ff0e7ecfe187f088d69954:343141
PASS: hashcat

MODE: 11
TYPE: Joomla < 2.5.18
HASH: b78f863f2c67410c41e617f724e22f34:89384528665349271307465505333378
PASS: hashcat

MODE: 12
TYPE: PostgreSQL
HASH: 93a8cf6a7d43e3b5bcd2dc6abb3e02c6:27032153220030464358344758762807
PASS: hashcat

MODE: 20
TYPE: md5($salt.$pass)
HASH: 57ab8499d08c59a7211c77f557bf9425:4247
PASS: hashcat

<SNIP>
```

You can scroll through the list and press `q` to exit.

The benchmark test (or performance test) for a particular hash type can be performed using the `-b` flag.

#### Hashcat - Benchmark

```shell-session
ChefBaki@htb[/htb]$ hashcat -b -m 0
hashcat (v6.1.1) starting in benchmark mode...

Benchmarking uses hand-optimized kernel code by default.
You can use it in your cracking session by setting the -O option.
Note: Using optimized kernel code limits the maximum supported password length.
To disable the optimized kernel code in benchmark mode, use the -w option.

OpenCL API (OpenCL 1.2 pocl 1.5, None+Asserts, LLVM 9.0.1, RELOC, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
=============================================================================================================================
* Device #1: pthread-Intel(R) Core(TM) i7-5820K CPU @ 3.30GHz, 4377/4441 MB (2048 MB allocatable), 6MCU

Benchmark relevant options:
===========================
* --optimized-kernel-enable

Hashmode: 0 - MD5


Speed.#1.........:   449.4 MH/s (12.84ms) @ Accel:1024 Loops:1024 Thr:1 Vec:8

Started: Fri Aug 28 21:52:35 2020
Stopped: Fri Aug 28 21:53:25 2020
```

For example, the hash rate for MD5 on a given CPU is found to be 450.7 MH/s.

We can also run `hashcat -b` to run benchmarks for all hash modes.

#### Hashcat - Optimizations

Hashcat has two main ways to optimize speed:

![Pasted-image-20230112205720.png](/Images/Pasted-image-20230112205720.png)

It is important to note that the use of `--force` should be avoided. While this appears to make `Hashcat` work on certain hosts, it is actually disabling safety checks, muting warnings, and bypasses problems that the tool's developers have deemed to be blockers. These problems can lead to false positives, false negatives, malfunctions, etc. If the tool is not working properly without forcing it to run with `--force` appended to your command, we should troubleshoot the root cause (i.e., a driver issue). Using `--force` is discouraged by the tool's developers and should only be used by experienced users or developers.