# macgen #

A tool for generating valid random [MAC addresses](http://en.wikipedia.org/wiki/MAC_address) for specific organizations. 

**[Mac's](http://en.wikipedia.org/wiki/Macintosh) are the best!**

Sorry, no. This has nothing to do with the [operating systems](http://en.wikipedia.org/wiki/Operating_system) [holy wars](http://dilbert.com/strips/comic/1995-06-24/). A MAC address is a "media access control" address. These are commonly used as the hardware addresses for networking devices in the [data link layer](http://en.wikipedia.org/wiki/Data_link_layer) of the [OSI networking model](http://en.wikipedia.org/wiki/OSI_reference_model). 

**So this thing will spoof an [IP address](http://en.wikipedia.org/wiki/Ip_address)?**

Sorry, still no. IP addresses work at layer three of the OSI model. MAC addresses are below that. Before you can get an IP address, your MAC address has to become known to the router for [ARP](http://en.wikipedia.org/wiki/Address_Resolution_Protocol) to work. 

Also, this tool doesn't ["spoof"](http://en.wikipedia.org/wiki/Spoofing_attack) anything. It only generates a random address in a way that makes the network connection look like it's coming from the organization or vendor of your choice. 

**Why do I need this?**

Modern network analysis tools, such as [Kismet](http://en.wikipedia.org/wiki/Kismet_%28software%29) and [Wireshark](http://en.wikipedia.org/wiki/Wireshark) perform a reverse lookup on a MAC address it sees on the network to determine what sort of a device it is. A pentester would be able to use this tool, in conjunction with a [MAC Spoofing technique](http://en.wikipedia.org/wiki/Mac_spoofing), in order to evade notice (either by casual observer or by an [IDS](http://en.wikipedia.org/wiki/Intrusion_Detection_System).)

**How do I MAC spoof?**

On [Linux](http://en.wikipedia.org/wiki/Linux), this functionality is built into the operating system, and accessible through the [ip](http://linux.die.net/man/8/ip) command:

	empty@monkey:~$ ip link show dev eth0
	2: eth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
	    link/ether 90:e2:ba:a1:95:70 brd ff:ff:ff:ff:ff:ff
	empty@monkey:~$ sudo ip link set eth0 addr 00:26:c7:60:5f:11
	empty@monkey:~$ ip link show dev eth0
	2: eth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
	    link/ether 00:26:c7:60:5f:11 brd ff:ff:ff:ff:ff:ff

**How do I use *macgen*?**

Here is an example for spoofing an old [DEC](http://en.wikipedia.org/wiki/Digital_Equipment_Corporation) networking device.

	empty@monkey:~$ sudo ip link set eth0 addr `macgen "digital electronics corp"`
	empty@monkey:~$ ip link show dev eth0
	2: eth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
	    link/ether 00:01:23:12:fe:cb brd ff:ff:ff:ff:ff:ff

As another example, to make your MAC address look like it is related to an [Advanced Persistent Threat](http://en.wikipedia.org/wiki/Advanced_persistent_threat), try the following:

	empty@monkey:~$ sudo ip link set eth0 addr `macgen china`
	empty@monkey:~$ ip link show dev eth0
	2: eth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
	    link/ether 00:0c:5d:e0:f2:61 brd ff:ff:ff:ff:ff:ff

You can confirm this by doing a reverse lookup:

	baigu@monkey:~$ macgen -r 00:0c:5d:e0:f2:61
	  00-0C-5D   (hex)		CHIC TECHNOLOGY (CHINA) CORP.

Finally, if you just want to see some of the organizations and a random MAC together, use the -v flag:

	empty@monkey:~$ macgen -v china
	Chinasys Technologies Limited
	00:12:0b:34:bc:0f

**How does this work?**

The [IEEE](http://en.wikipedia.org/wiki/Ieee) is a standards organization that maintains the list of all [MAC address prefixes](http://standards.ieee.org/develop/regauth/oui/). The maintain a public copy called the [oui.txt](http://standards.ieee.org/develop/regauth/oui/oui.txt) file. *macgen* is just a [Perl](http://www.perl.org/) script that examines your copy of the oui.txt file, and uses [grep](http://perldoc.perl.org/functions/grep.html) to match the string you give it to an organizations name. You will need to download your own copy of the oui.txt file.

## Usage ##

	empty@monkey:~$ macgen --help
	usage: ./macgen [-delimiter CHAR][-uppercase][-verbose][-file OUI_FILE][-help] [GREP_STRING]
		GREP_STRING : Only use organizations which match GREP_STRING.
		-delimiter CHAR : Use the CHAR character as the delimiter. (Default is ':'.)
		-uppercase : Use uppercase hex. (Default is lowercase.)
		-verbose : Print the "Organization" name in addition to the mac.
		-file OUI_FILE : Use OUI_FILE as the IEEE oui.txt file. (Default is "/etc/oui.txt".)
		-reverse MAC : Return the oui information for the given MAC address.
		-help : Print this message.
	
	Example:
		empty@monkey:~$ macgen -v china
		Sihinwa Industries(China) Ltd.
		00:1d:86:45:0f:e5

