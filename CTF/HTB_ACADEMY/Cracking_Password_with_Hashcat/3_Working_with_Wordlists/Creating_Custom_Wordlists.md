
## Wordlists

During an assessment, we may retrieve one or more password hashes that are crucial to the engagement's success. Despite our best attempts, these hashes cannot be cracked with common wordlists using the dictionary, combination, mask, or hybrid attacks covered in the prior sections. It may be necessary to create a custom, targeted wordlist to achieve our goal in these instances.

It is necessary to spend time refining a wordlist because the success rate heavily depends on it. Wordlists can be obtained from various sources and customized based on the target and further fine-tuned using rules. Wordlists can be found for passwords, usernames, file names, payloads, and many other data types. The [SecLists](https://github.com/danielmiessler/SecLists) repository also contains many wordlists useful for username enumeration password identification.

---

## Creating Wordlists

Many open-source tools help in creating customized password wordlists based on our requirements.

---

## Crunch

Crunch can create wordlists based on parameters such as words of a specific length, a limited character set, or a certain pattern. It can generate both permutations and combinations.

It is installed by default on Parrot OS and can be found [here](https://sourceforge.net/projects/crunch-wordlist/). The general syntax of `crunch` is as follows:

#### Crunch - Syntax

```shell-session
ChefBaki@htb[/htb]$ crunch <minimum length> <maximum length> <charset> -t <pattern> -o <output file>
```

The "`-t`" option is used to specify the pattern for generated passwords. The pattern can contain "`@`," representing lower case characters, "`,`" (comma) will insert upper case characters, "`%`" will insert numbers, and "`^`" will insert symbols.

#### Crunch - Generate Word List

```shell-session
ChefBaki@htb[/htb]$ crunch 4 8 -o wordlist
```

The command above creates a wordlist consisting of words with a length of 4 to 8 characters, using the default character set.

Let's assume that Inlane Freight user passwords are of the form "`ILFREIGHTYYYYXXXX`," where "`XXXX`" is the employee ID containing letters, and "`YYYY`" is the year. We can use `crunch` to create a list of such passwords.

#### Crunch - Create Word List using Pattern

```shell-session
ChefBaki@htb[/htb]$ crunch 17 17 -t ILFREIGHT201%@@@@ -o wordlist
```

The pattern "`ILFREIGHT201%@@@@`" will create words with the years 2010-2019 followed by four letters. The length here is 17, which is constant for all words.

If we know a user's birthdate is 10/03/1998 (through social media, etc.), we can include this in their password, followed by a string of letters. Crunch can be used to create a wordlist of such words. The "`-d`" option is used to specify the amount of repetition.

#### Crunch - Specified Repetition

```shell-session
ChefBaki@htb[/htb]$ crunch 12 12 -t 10031998@@@@ -d 1 -o wordlist
```

## CUPP

`CUPP` stands for `Common User Password Profiler`, and is used to create highly targeted and customized wordlists based on information gained from social engineering and OSINT. People tend to use personal information while creating passwords, such as phone numbers, pet names, birth dates, etc. CUPP takes in this information and creates passwords from them. These wordlists are mostly used to gain access to social media accounts. CUPP is installed by default on Parrot OS, and the repo can be found [here](https://github.com/Mebus/cupp). The "`-i`" option is used to run in interactive mode, prompting `CUPP` to ask us for information on the target.

#### CUPP - Usage Example

```shell-session
ChefBaki@htb[/htb]$ python3 cupp.py -i

[+] Insert the information about the victim to make a dictionary
[+] If you don't know all the info, just hit enter when asked! ;)

> First Name: roger
> Surname: penrose
> Nickname:      
> Birthdate (DDMMYYYY): 11051972

> Partners) name: beth
> Partners) nickname:
> Partners) birthdate (DDMMYYYY):

> Child's name: john
> Child's nickname: johnny
> Child's birthdate (DDMMYYYY):

> Pet's name: tommy
> Company name: INLANE FREIGHT

> Do you want to add some key words about the victim? Y/[N]: Y
> Please enter the words, separated by comma. [i.e. hacker,juice,black], spaces will be removed: sysadmin,linux,86391512
> Do you want to add special chars at the end of words? Y/[N]:
> Do you want to add some random numbers at the end of words? Y/[N]:
> Leet mode? (i.e. leet = 1337) Y/[N]:

[+] Now making a dictionary...
[+] Sorting list and removing duplicates...
[+] Saving dictionary to roger.txt, counting 2419 words.
[+] Now load your pistolero with roger.txt and shoot! Good luck!
```

The command above shows how the data for the user Roger Penrose, was provided to CUPP. The unknown fields can be just left empty. After taking in all data, CUPP creates a wordlist based on it. It also supports appending random characters and a "Leet" mode, which uses combinations of letters and numbers in common words. `CUPP` can also fetch common names from various online databases using the "`-l`" option.

---

## KWPROCESSOR

`Kwprocessor` is a tool that creates wordlists with keyboard walks. Another common password generation technique is to follow patterns on the keyboard. These passwords are called keyboard walks, as they look like a walk along the keys. For example, the string "`qwertyasdfg`" is created by using the first five characters from the keyboard's first two rows. This seems complex to the normal eye but can be easily predicted. `Kwprocessor` uses various algorithms to guess patterns such as these.

The tool can be found [here](https://github.com/hashcat/kwprocessor) and has to be installed manually.

#### Kwprocessor - Installation

```shell-session
ChefBaki@htb[/htb]$ git clone https://github.com/hashcat/kwprocessor
ChefBaki@htb[/htb]$ cd kwprocessor
ChefBaki@htb[/htb]$ make
```

The help menu shows the various options supported by kwp. The pattern is based on the geographical directions a user could choose on the keyboard. For example, the "`--keywalk-west`" option is used to specify movement towards the west from the base character. The program takes in base characters as a parameter, which is the character set the pattern will start with. Next, it needs a keymap, which maps the locations of keys on language-specific keyboard layouts. The final option is used to specify the route to be used. A route is a pattern to be followed by passwords. It defines how passwords will be formed, starting from the base characters. For example, the route 222 can denote the path 2 * EAST + 2 * SOUTH + 2 * WEST from the base character. If the base character is considered to be "`T`" then the password generated by the route would be "`TYUJNBV`" on a US keymap. For further information, refer to the [README](https://github.com/hashcat/kwprocessor#routes) for kwprocessor.

#### Kwprocessor - Example









