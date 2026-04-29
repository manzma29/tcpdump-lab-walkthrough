**TCPDump Lab Walkthrough**

**What is this lab about?**

In this lab, we use a tool called tcpdump to look at recorded network traffic. Think of network traffic like cars driving on a highway tcpdump lets you pull over to the side of the road and watch what's going by, where it's going, and what it's carrying.

We're not capturing live traffic. Instead, we're reading from a pre-recorded file called a **pcap** (packet capture) that Antisyphon Labs has already provided for you. This is like watching a recording of the highway instead of watching it live.

1. Get into the root shell

When you first open a terminal, you're logged in as a regular user. tcpdump needs elevated permissions to work. Run this:

- sudo su -

You'll be asked for your password. After entering it, your prompt will change, you're now the root user. Think of this like unlocking admin mode.

1. Go to the right folder

Navigate to the folder where the lab's pcap file lives:

- cd /opt/tcpdump

cd stands for "change directory." The file magnitude\_1hr.pcap is already here, provided by Antisyphon Labs no need to download anything.

1. Filter by a specific IP address

Let's run our first command. We're going to look at all traffic involving a specific computer on the network, identified by its IP address:

- tcpdump -n -r magnitude\_1hr.pcap host 192.168.99.52



Here's what each part means:

|-n|Don't look up hostnames — just show raw IP addresses. Faster and cleaner.|
| :- | :- |
|-r|Read from a file instead of live traffic.|
|host ...|Only show packets that involve this IP address.|

Each line in the output is one packet, showing: its timestamp, protocol, source IP/port, destination IP/port, TCP flags, and data size.

1. Narrow it down by port

An IP address tells you which computer, but a **port** tells you which service on that computer. Port 80 is standard web traffic (HTTP):

- tcpdump -n -r magnitude\_1hr.pcap host 192.168.99.52 and port 80

Now you're only seeing web traffic between that IP and port 80.

1. Read the actual content

So far, we've been looking at metadata the envelope, not the letter inside. The -A flag shows the actual content of each packet in readable text:

- tcpdump -n -r magnitude\_1hr.pcap host 192.168.99.52 and port 80 -A

Tip: This will scroll a lot. Press Ctrl + C after a few seconds to stop it.

You should see actual HTTP GET requests and responses. Look for the packet at timestamp 15:14:32.638976 you'll spot **PowerShell** commands and **Base64-encoded data**. That's a major red flag. Attackers use Base64 to disguise what they're doing.

1. See the raw hex data too

Add -X alongside -A to also see the raw hexadecimal values, which are useful for analyzing binary or encoded content:

- tcpdump -n -r magnitude\_1hr.pcap host 192.168.99.52 and port 80 -AX

Tip: Press Ctrl + C after a few seconds.


1. Filter by protocol (IPv6)

You can filter by protocol type. Let's look at all IPv6 traffic in the pcap:

- tcpdump -n -r magnitude\_1hr.pcap ip6

IPv6 is the newer version of IP addressing. Many networks run IPv4 and IPv6 at the same time.

1. Filter by a whole network range

Instead of one IP, you can watch an entire range. This helps answer: "Is anything else on this network talking to suspicious addresses?"

- tcpdump -n -r magnitude\_1hr.pcap net 192.168.99.0/24

The /24 means all addresses from 192.168.99.0 to 192.168.99.255 an entire subnet. Attackers sometimes spread traffic across multiple systems to avoid detection, and this filter helps you spot that pattern.







