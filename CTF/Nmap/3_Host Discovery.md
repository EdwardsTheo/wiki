When we need to conduct an internal penetration test for the entire network of a company, for example, then we should, first of all, get an overview of which systems are online that we can work with. To actively discover such systems on the network, we can use various `Nmap` host discovery options. There are many options `Nmap` provides to determine whether our target is alive or not. The most effective host discovery method is to use **ICMP echo requests**, which we will look into.

It is always recommended to store every single scan. This can later be used for comparison, documentation, and reporting. After all, different tools may produce different results. Therefore it can be beneficial to distinguish which tool produces which results.

### Scan Network Range

```shell-session
ChefBaki@htb[/htb]$ sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d" " -f5

10.129.2.4
10.129.2.10
10.129.2.11
10.129.2.18
10.129.2.19
10.129.2.20
10.129.2.28
```

![Pasted-image-20221219151956.png](/Images/Pasted-image-20221219151956.png)
This scanning method works only if the firewalls of the hosts allow it. Otherwise, we can use other scanning techniques to find out if the hosts are active or not. We will take a closer look at these techniques in "`Firewall and IDS Evasion`".

## Scan IP List

During an internal penetration test, it is not uncommon for us to be provided with an IP list with the hosts we need to test. `Nmap` also gives us the option of working with lists and reading the hosts from this list instead of manually defining or typing them in.

Such a list could look something like this:

```shell-session
ChefBaki@htb[/htb]$ cat hosts.lst

10.129.2.4
10.129.2.10
10.129.2.11
10.129.2.18
10.129.2.19
10.129.2.20
10.129.2.28
```


If we use the same scanning technique on the predefined list, the command will look like this:

```shell-session
ChefBaki@htb[/htb]$ sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d" " -f5

10.129.2.18
10.129.2.19
10.129.2.20
```

![Pasted-image-20221219152104.png](/Images/Pasted-image-20221219152104.png)

In this example, we see that only 3 of 7 hosts are active. Remember, this may mean that the other hosts ignore the default **ICMP echo requests** because of their firewall configurations. Since `Nmap` does not receive a response, it marks those hosts as inactive.

## Scan Multiple IPs

It can also happen that we only need to scan a small part of a network. An alternative to the method we used last time is to specify multiple IP addresses.

![Pasted-image-20221219152123.png](/Images/Pasted-image-20221219152123.png)
If these IP addresses are next to each other, we can also define the range in the respective octet.

![Pasted-image-20221219152146.png](/Images/Pasted-image-20221219152146.png)
## Scan Single IP

Before we scan a single host for open ports and its services, we first have to determine if it is alive or not. For this, we can use the same method as before.

![Pasted-image-20221219152205.png](/Images/Pasted-image-20221219152205.png)

![Pasted-image-20221219152225.png](/Images/Pasted-image-20221219152225.png)
If we disable port scan (`-sn`), Nmap automatically ping scan with `ICMP Echo Requests` (`-PE`). Once such a request is sent, we usually expect an `ICMP reply` if the pinging host is alive. The more interesting fact is that our previous scans did not do that because before Nmap could send an ICMP echo request, it would send an `ARP ping` resulting in an `ARP reply`. We can confirm this with the "`--packet-trace`" option. To ensure that ICMP echo requests are sent, we also define the option (`-PE`) for this.

![Pasted-image-20221219152251.png](/Images/Pasted-image-20221219152251.png)

![Pasted-image-20221219152302.png](/Images/Pasted-image-20221219152302.png)
Another way to determine why Nmap has our target marked as "alive" is with the "`--reason`" option.

![Pasted-image-20221219152318.png](/Images/Pasted-image-20221219152318.png)
We see here that `Nmap` does indeed detect whether the host is alive or not through the `ARP request` and `ARP reply` alone. To disable ARP requests and scan our target with the desired `ICMP echo requests`, we can disable ARP pings by setting the "`--disable-arp-ping`" option. Then we can scan our target again and look at the packets sent and received.

![Pasted-image-20221219152332.png](/Images/Pasted-image-20221219152332.png)
We have already mentioned in the "`Learning Process`," and at the beginning of this module, it is essential to pay attention to details. An `ICMP echo request` can help us determine if our target is alive and identify its system. More strategies about host discovery can be found at: