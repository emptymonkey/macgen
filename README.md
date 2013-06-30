# macgen #

*macgen* is a tool for generating valid random [MAC addresses](http://en.wikipedia.org/wiki/MAC_address) for specific organizations. 

**[Mac's](http://en.wikipedia.org/wiki/Macintosh) are the best!**

Sorry, but this has nothing to do with the [operating system](http://en.wikipedia.org/wiki/Operating_system) [holy wars](http://dilbert.com/strips/comic/1995-06-24/). "MAC" in this context means "media access control", and refers to the hardware address of a network device. A MAC address is the host's network address within the [data link layer](http://en.wikipedia.org/wiki/Data_link_layer) of the [OSI networking model](http://en.wikipedia.org/wiki/OSI_reference_model). The MAC address is used in conjunction with [ARP](http://en.wikipedia.org/wiki/Address_Resolution_Protocol) to communicate on layer two.

The first three [octets](http://en.wikipedia.org/wiki/Octet_%28computing%29) of a MAC address make up the [OUI](http://en.wikipedia.org/wiki/Organizationally_Unique_Identifier). The OUI of the MAC address determines which vendor manufactured that particular network device. The second three octets are used as a unique identifier as determined by the vendor.

The OUI's themselves are registered by the vendors with the [IEEE](http://en.wikipedia.org/wiki/Ieee). The IEEE is a standards organization that maintains the list of all [MAC address prefixes](http://standards.ieee.org/develop/regauth/oui/). They maintain a public copy in a file called [oui.txt](http://standards.ieee.org/develop/regauth/oui/oui.txt).

*macgen* is a [Perl](http://www.perl.org/) script that examines your copy of the oui.txt file, and uses [grep](http://perldoc.perl.org/functions/grep.html) to match the string you give it with an organizations name. It then returns the first three octets from your match, and [randomly](http://search.dilbert.com/comic/Random%20Number%20Generator) generates the second three octets.

You will need to download your own copy of the oui.txt file for *macgen* to work.

**When would I use *macgen*?**

Modern network analysis tools, such as [Kismet](http://en.wikipedia.org/wiki/Kismet_%28software%29) and [Wireshark](http://en.wikipedia.org/wiki/Wireshark) perform a reverse OUI lookup on the MAC addresses they see on the network for the purpose of reporting the device type. A pentester would be able to use *macgen*, in conjunction with a [MAC address spoofing technique](http://en.wikipedia.org/wiki/Mac_spoofing), to evade detection (either by a casual observer or even by a more rigorous [IDS](http://en.wikipedia.org/wiki/Intrusion_Detection_System).)

**How do I do MAC address spoofing?**

On [Linux](http://en.wikipedia.org/wiki/Linux), this functionality is built into the operating system and is accessible through the [ip](http://linux.die.net/man/8/ip) command:

	empty@monkey:~$ ip link show dev eth0
	2: eth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
	    link/ether 90:e2:ba:a1:95:70 brd ff:ff:ff:ff:ff:ff
	empty@monkey:~$ sudo ip link set eth0 addr 00:26:c7:60:5f:11
	empty@monkey:~$ ip link show dev eth0
	2: eth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
	    link/ether 00:26:c7:60:5f:11 brd ff:ff:ff:ff:ff:ff

**Ewwww! Perl?! Are you old or something?? Why didn't you just use (python|ruby|lisp|javascript) to do this?? All the cool kids are doing it!**

Listen up, whippersnapper. I learned Perl long ago when it too was fashionable. It meets all of my [data munging](http://en.wikipedia.org/wiki/Data_munging) needs and I haven't had a compelling reason to switch yet. It's readily available on most systems and gets the job done, so stop yer [fanboi](http://www.urbandictionary.com/define.php?term=fanboi) yappin! That, and [get off my lawn](http://en.wikipedia.org/wiki/You_kids_get_off_my_lawn!)!

## Usage ##

	empty@monkey:~$ macgen --help
	usage: ./macgen [-delimiter CHAR][-uppercase][-verbose][-file OUI_FILE][-help] [GREP_STRING]
		GREP_STRING : Only use organizations which match GREP_STRING.
		-delimiter CHAR : Use the CHAR character as the delimiter. (Default is ':'.)
		-uppercase : Use uppercase hex. (Default is lowercase.)
		-verbose : Print the "Organization" name in addition to the mac.
		-file OUI_FILE : Use OUI_FILE as the IEEE oui.txt file. (Default is "/etc/oui.txt".)
		-reverse MAC : Return the organization name for the given MAC address.
		-help : Print this message.
	
## Examples ##

Here is an example for spoofing an old [DEC](http://en.wikipedia.org/wiki/Digital_Equipment_Corporation) networking interface:

	empty@monkey:~$ sudo ip link set eth0 addr `macgen "digital electronics corp"`
	empty@monkey:~$ ip link show dev eth0
	2: eth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
	    link/ether 00:01:23:12:fe:cb brd ff:ff:ff:ff:ff:ff

In this example, we will demonstrate how a MAC address can be made to impersonate an [Advanced Persistent Threat](http://en.wikipedia.org/wiki/Advanced_persistent_threat), and why you shouldn't believe everything you read in a log file:

	empty@monkey:~$ sudo ip link set eth0 addr `macgen "shanghai.*telecom"`
	empty@monkey:~$ ip link show dev eth0
	2: eth0: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN qlen 1000
	    link/ether 78:ec:22:52:29:56 brd ff:ff:ff:ff:ff:ff

For confirmation, perform a reverse lookup:

	empty@monkey:~$ macgen -r 78:ec:22:52:29:56
	78-EC-22   (hex)		Shanghai Qihui Telecom Technology Co., LTD

Note the use of a basic [regex]() in that last example. If you are familiar with [Perl's regular expressions], go ahead and use them here:
	
	


