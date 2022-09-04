*In this walkthrough, I will follow along the free Nmap room in TryHackMe and I will work through the questions that are seen along the way. A link to the room is [here](https://tryhackme.com/room/furthernmap). Nmap is a open-source Linux command-line utility tool that is used for security auditing and network scanning purposes. It is commonly known as the industry standard for port scanning and it is extremely powerful.*

## Table of Contents
1. [Task 1](#task1)
2. [Task 2](#task2)
3. [Task 3](#task3)
4. [Task 4](#task4)
5. [Task 5](#task5)
6. [Task 6](#task6)
7. [Task 7](#task7)
8. [Task 8](#task8)
9. [Task 9](#task9)
10. [Task 10](#task10)
11. [Task 11](#task11)
12. [Task 12](#task12)
13. [Task 13](#task13)
14. [Task 14](#task14)
15. [Task 15](#task15)

## Task 1 - Deploy <a name="task1"></a>

**End Questions**
- There is no answers required to the question here, just deploy the machine.
- I prefer to connect via OpenVPN and use my own Kali Linux based machine in a VMware Workstation.
- Also, note that this machine is for scanning purposes only. You do not need to log into it, or exploit any vulnerabilities to gain access.

![image1.png](https://github.com/starscreamF22/TryHackMe/blob/main/images/image1.png)

## Task 2 - Introduction <a name="task2"></a>

- The more knowledge we have about a system/network, the better. Proper enumeration is key when doing scans.

- The first stage in assessing a target is port scanning. We need to establish which services are running on the target.

- Ports are necessary for making multiple network requests or having multiple services available.

- Network connections are made using 2 ports, where there is an entry and exit point. If I want to connect to a webpage, my computer may use a port number (usually a very high and random number) like 47523 and the server I want to connect to will use port 443 for HTTPS connections.

- Every computer has a total of 65,535 available ports but many of these are registered as standard ports. Windows NETBIOS can be found on port 139 and SMB can be found on port 445. Standard ports can be altered however, so it is not unheard for these standard ports to be altered.

- Using Nmap effectively, we can connect to ports, see how it responds and see if it is open/closed/filtered.

- Once we see which ports are open, we can then try see what services are running, using Nmap.
 
**End Questions**

- The answers to the questions at the end are straight forward enough and found in the text in the room.

![image2.jpg](attachment:image2.jpg)

## Task 3 - Nmap Switches <a name="task3"></a>

<p>We run Nmap from a terminal, in my case via Kali Linux, where it is installed by default. Nmap switches refer to the arguements we give Nmap to do various things.</p>

- We can type in ```nmap``` in our terminal and we see some of the different switches.
- We can type in ```nmap -h``` for some help options.

![image3.png](attachment:image3.png)

![image4.png](attachment:image4.png)

- We can also view the nmap manual by typing in ```man nmap``` and you see this.

![image5.png](attachment:image5.png)

**End Questions**

- The answer for the first 2 questions is in the "SCAN TECHNIQUES" section when you type in ```nmap -h```
- A Syn Scan is the default and most popular scan in Nmap and is relitively unobtrusive and stealthy as it never completes the TCP connection.
- A UDP scan is slower and more difficult than TCP scans.

![image6.png](attachment:image6.png)

![image7.jpg](attachment:image7.jpg)

- The next 8 answers are also found in the command ```nmap -h```
- You just have to search through the sections to find them, but they are there!

![image8.jpg](attachment:image8.jpg)

- **Question 11:** We need to set the Nmap timing template to level 5. There is various templates, -T0, -T1, -T2, -T3, -T4, -T5 and each scan accelerates in speed. But higher speed scans are noisier and can incur errors. We would set the timing template to level 5 by typing in ```-T5```

- **Question 12:** To tell Nmap to only scan port 80, we would use ```-p 80```

- **Question 13:** To tell Nmap to only scan ports 1000 to 1500, we would use ```-p 1000-1500```

- **Question 14:** To tell Nmap to scan all ports we would use ```-p-```

- **Question 15:** To activate a script from the Nmap scripting library we would use ```--script```

- **Question 16:** To activate all scripts in the 'vuln' category we would use ```--script=vuln```

![image9.png](attachment:image9.png)

## Task 4 - Scan Types - Overview <a name="task4"></a>

<p> When port scanning with Nmap, there is 3 basic scan types. There is TCP Connect Scans (-sT), SYN "Half-open" Scans (-sS) and UDP Scans (-sU).</p>
<p> There are also other less common port scan types, which won't be covered much in this Nmap room but are useful to be aware of. These are TCP Null Scans (-sN), TCP FIN Scans (-sF) and TCP Xmas Scans (-sX).</p>
<p> We will also look at ICMP scanning, which is known as a 'ping'.</p>

**End Questions**

- There is no answers required to the questions here, just read through the scan types and click submit.

![image10.png](attachment:image10.png)

## Task 5 - Scan Types - TCP Connect Scans <a name="task5"></a>

<p> Here, we will look at TCP Connect Scans (-sT), which are one of the 3 basic scan types as mentioned in Task 4. TCP Connect scans work by performing the 3-way handshake with each target port we have chosen. Nmap determines whether the service is open or closed by the response it receives. How does a TCP 3-way handkshake work though?</p>

*A TCP 3-way-handshake has 3 stages and here is a basic idea of the process*

- 1.My computer sends a TCP Request to the target server with a SYN flag set.
- 2.The server acknowledges this packet with a TCP Response containing the SYN flag set aswell as the ACK flag.
- 3.My computer completes the 3-way-handshake by sending back a TCP Request with the ACK flag set.

Here is a visual of the process from TryHackMe where my computer is the client.

![image11.png](attachment:image11.png)

- RFCs produced by the IETF (Internet Engineering Task Force) cover many aspects of computer networking. They describe the Internet's technical foundations, such as addressing, routing, and transport technologies.
- According to RFC 793, when a port is closed:<blockquote class="blockquote">"... If the connection does not exist (CLOSED) then a reset is sent in response to any incoming segment except another reset.  In particular, SYNs addressed to a non-existent connection are rejected by this means."</blockquote>
- In other words, Nmap sends a TCP Request with the SYN flag set to a closed port, the server will respond with a TCP packet with the RST (Reset) flag set. We can therefore know that a port is closed if we see this.
- If Nmap sends a TCP Request to an open port, the target server responds with a TCP packet with the SYN/ACK flags set, indicating it is open and completes the handshake by sending back a TCP packet with ACK set.
- But what if the port is behind a firewall but is open?
- Many firewalls are configured to simply drop incoming packets as they encounter them, so Nmap sending out a TCP SYN Request and getting nothing back is an indicator of a port behind a firewall, which we call a filtered port.
- But firewalls can easily be configured to respond with a RST TCP packet for example, so we would not get an accurate reading of the target port.

**End Questions**

- These are fairly straightforward and the answers are contained in the information in the room.

![image12.jpg](attachment:image12.jpg)

## Task 6 - Scan Types - SYN Scans <a name="task6"></a>

<p> Now, we take a look at TCP SYN scans (-sS). They are often reffered to as "Half-open" scans, or "Stealth" scans. The connection is never fully established and the 3-way-handshake for a SYN scan is slightly different to a TCP scan.</p>

*The 3-way handshake process for a SYN scan is as follows:*

- 1.My computer sends a TCP Request to the target server with a SYN flag set.
- 2.The server acknowledges this packet with a TCP Response containing the SYN flag set aswell as a ACK flag.
- 3.The 3-way-handshake is complete when my computer sends back a RST TCP packet after receiving a SYN/ACK from the server.

<p>Here is a visual of the process from TryHackMe where my computer is the client.<p>

![image13.png](attachment:image13.png)

- SYN scans have both pros and cons

*Pros*
- Older Intrusion Detection systems look for a full 3-way handshake, which does not occur in a SYN scan.
- SYN scans are often not logged by applications listening on open ports, as standard practice is to log a connection once it's been fully established. This plays into the idea of SYN scans being stealthy.
- SYN scans are faster than a TCP Connect scan as a full 3-way handshake does not have to occur for every port.

*Cons*

- SYN scans need to be run with sudo permissions in Linux. SYN scans require the ability to create raw packets (as opposed to the full TCP handshake),  which is a privilege only the root user has by default.
- Unstable services are sometimes brought down by SYN scans, which could prove problematic if a client has provided a production environment for the test.

<p> SYN scans are the defualt scan used by Nmap if run with sudo permissions. If run without sudo permissions, Nmap defaults to the TCP Connect scan.</p>
<p>  When using a SYN scan to identify closed and filtered ports, the exact same rules as with a TCP Connect scan apply. If a port is closed then the server responds with a RST TCP packet. If the port is filtered by a firewall then the TCP SYN packet is either dropped, or spoofed with a TCP reset.</p>
<p> Basically, the two scans are identical: the big difference is in how they handle open ports.</p>

**End Questions**

- These are fairly straightforward again and the answers are contained in the information in the room.

![image14.jpg](attachment:image14.jpg)

## Task 7 - Scan Types - UDP Scans <a name="task7"></a>

<p> UDP scans (-sU) are different to the two TCP scans we looked at above. UDP scans are stateless, meaning rather than going back and forth with a 3-way-handshake, UDP connections send packets and hope they make it there. They use a don't think, just throw mentality. This means they are very quick but the lack of acknowledgement makes it much more difficult to use when scanning.</p>

- When a packet is sent to an open UDP port, there should be no response. In this case, Nmap refers to the port as ```Open|Filtered```, meaning the port might be open, but could be firewalled.
- If it gets a UDP response (which is very unusual), then the port is marked as open.
- More commonly though there is no response, in which case the request is sent a second time as a double-check. If there is still no response then the port is marked ```Open|Filtered``` and Nmap moves on.
- Closed ports should respond with an ICMP (ping) packet containing a message that the port is unreachable. This clearly identifies closed ports, which Nmap marks as such and moves on.

<p> Since UDP scans don't really tell us whether a UDP port is open, it is much slower than the various TCP scans. It may take roughly 20 minutes to scan the first 1000 ports with a good connection.</p>

- You could run an Nmap UDP scan for the top 20 most commonly used UDP ports to reduce scan time. This would be done via the command ```nmap -sU --top-ports 20 <target>```

- For common UDP ports, the UDP scan will send a specific payload which is more likely to elicit a response from which a more accurate result can be drawn, instead of the usual raw UDP packets it usually sends.

**End Questions**

- The answers are contained in the information in the room so should be easy to answer.

![image15.jpg](attachment:image15.jpg)

## Task 8 - Scan Types - NULL, FIN and Xmas <a name="task8"></a>

<p> NULL, FIN and Xmas TCP port scans are less commonly than the others mentioned thus far. The NULL, FIN and Xmas TCP port scans tend to be even stealthier than a SYN scan.</p>

<p> Null scans (-sN) occur when the TCP Request is sent with no flag set. The target host should respond with a RST if the port is closed.</p>

<p> FIN scans (-sF) are similar but instead of sending an empty packet, a Request is sent with the FIN flag (usually used to gracefully close an active connection). We expect a RST Response if the port is closed.</p>

<p> Xmas scans (-sX) send a malformed TCP packet and expects a RST Response for closed ports. It's referred to as an Xmas scan as the flags that it sets (PSH, URG and FIN) give it the appearance of a blinking christmas tree when viewed as a packet capture in Wireshark (a network analysis tool). </p>

- The expected response for open ports with these scans is identical. If the port is open then there is no response to the malformed packet. This is also the expected behaviour if the port is firewalled, so these 3 scans will only ever identify ports as ```open|filtered```, ```closed``` or ```filtered```. If a port is identified as filtered with one of these scans then it is usually because the target has responded with an ICMP unreachable packet.
- Microsoft Windows (and a lot of Cisco network devices) are known to respond with a RST to any malformed TCP packet, regardless of whether the port is actually open or not. This results in all ports showing up as being closed regardless of the scans used.
- Many firewalls will drop incoming TCP packets to blocked ports which have the SYN flag set (thus blocking new connection initiation requests). If we send requests which do not contain the SYN flag set, we can try bypass this kind of firewall.
- But most Intrusion Detection Systems are savvy to these types of scans so we cannot rely on them to be 100% effective with modern systems.

**End Questions**

- The answers are contained in the text in the room so should be easy enough to answer.

![image16.jpg](attachment:image16.jpg)

## Task 9 - Scan Types - ICMP Network Scanning <a name="task9"></a>

<p> On first connection to a target network, we want a 'map' of the network structure ie: see which ip addresses conatin active hosts, and which do not not.</p>
<p> We can do this via a 'ping sweep', where Nmap sends a ICMP packet to each possible ip address for the target network. When it receives a response, it marks the IP address that responded as being alive. This is not always accurate however but can provide something of a baseline.</p>

- To perform the ping sweep, you use the switch ```-sn``` in conjunction with IP ranges you are targeting with a hypen and the number of hosts or using CIDR notaion. 
- So you could scan the network ```192.168.0.x``` using ```nmap -sn 192.168.0.1-254``` or ```nmap -sn 192.168.0.0/24```
- The ```-sn``` tells Nmap not to scan any ports, just to rely primarily on ICMP echo packets (or ARP requests on a local network, if run with sudo permissions or directly as the root user) to identify targets.
- ```-sn``` will also cause Nmap to send a TCP SYN packet to port 443 (HTTPS) of the target, as well as a TCP ACK (or TCP SYN if not run as root) packet to port 80 (HTTP) of the target.

**End Questions**

- **Question 1:** *How would you perform a ping sweep on the 172.16.x.x network (Netmask: 255.255.0.0) using Nmap? (CIDR notation)*
- If you internet search 'CIDR notation table' you will find that 172.16.x.x would fall in the /16 range, which we are also told in the hint, so the answer to this question woule be ```nmap -sn 172.16.0.0/16```

![image17.png](attachment:image17.png)

## Task 10 - NSE Scripts - Overview <a name="task10"></a>

<p> The Nmap Scripting Engine (NSE) is an incredibly powerful addition to Nmap, extending its functionality quite considerably. NSE Scripts are written in the Lua programming language, and can be used to do a variety of things: from scanning for vulnerabilities, to automating exploits for them. The NSE is particularly useful for reconnaisance, however, it is well worth bearing in mind how extensive the script library is.</p>
<p> There are many categories available. Some useful categories include:</p>

- ```safe```: Will not affect target
- ```intrusive```: Not safe, likely to affect the target
- ```vuln```: Scan for vulnerabilities
- ```exploit```: Attempt to exploit a vulnerability
- ```auth```: Attempt to bypass authentication for running services
- ```brute```: Attempt to bruteforce credentials for running services
- ```discovery```: Attempt to query running services for further information about the network ( like to query an SNMP server)

**End Questions**

- Pretty straightforward answers that can be found in the room.

![image18.jpg](attachment:image18.jpg)

## Task 11 - NSE Scripts - Working with the NSE <a name="task11"></a>

- As we saw in Task 3, we saw the switch for activating NSE scripts in the 'vuln' category, which was ```--script=vuln```
- Other catagories work in the same way, so if the command  ```--script=safe``` is used, then any applicable safe scripts will be run against the target.
- So, to run a certain script, we would use ```--script=<script-name>``` like ```--script=http-fileupload-exploiter``` 
- We can run multiple scripts at the same time by seperating them using a comma like ```--script=smb-enum-users,smb-enum-shares```
- Some scripts need certain arguements which can be given with the ```--script-args``` Nmap switch. An example of this would be the ```http-put``` script, which takes two arguments: the URL to upload the file to, and the file's location on disk. An example of this would be ```nmap -p 80 --script http-put --script-args http-put.url='/dav/shell.php',http-put.file='./shell.php'```
- Arguments are separated by commas, and connected to the corresponding script with periods i.e. ```<script-name>.<argument>```
- Nmap scripts come with built in help menus which can be accessed via the command ```nmap --script-help <script-name>```

**End Questions**

- **Question 1:** *What optional argument can the ```ftp-anon.nse``` script take?*
- If we go to https://nmap.org/ and we search 'ftp-anon' and click the first link that appears, we are brought to https://nmap.org/nsedoc/scripts/ftp-anon.html where we see that the optional arguement that the script can take is 'maxlist'

![image19.png](attachment:image19.png)

## Task 12 - NSE Scripts - Searching for Scripts <a name="task12"></a>

- To search for scripts, we can use the Nmap website which has a list of all official scripts or we can search local storage on our attacking machine. Nmap stores it's scripts on Linux, which we can see by changing to the directory via the command ```/usr/share/nmap/scripts``` and seeing what scripts are there.

![image20.png](attachment:image20.png)

- We will use the script ```script.db``` to explore the searching of scripts. This is a formatted text file containing filenames and categories for each available script.

- First we will check the file type via the command ```file script.db```
- Then we will check the first 10 lines of the file, using the command ```head script.db```
- We can use grep to search for text or files. Here we will see where the word 'ftp' appears in scripts with the command ```grep "ftp" script.db```. We can also search for scripts using ```ls```
- The same techniques can also be used to search for categories of script with the command ```grep "safe" script.db```

![image21.png](attachment:image21.png)

- If we are missing a script, we can use the command ```sudo apt update && sudo apt install nmap``` to make sure the latest version of Nmap is installed and we can also manually download scripts from Nmap with the command ```sudo wget -O /usr/share/nmap/scripts/<script-name>.nse https://svn.nmap.org/nmap/scripts/<script-name>.nse```. We must then follow this up with ```nmap --script-updatedb```, which updates the ```script.db``` file.
- If you were to make your own NSE script and add it into Nmap, you would require the same "updatedb" command to load it in.

- **Question 1:** *Search for "smb" scripts in the ```/usr/share/nmap/scripts/``` directory using either of the demonstrated methods. What is the filename of the script which determines the underlying OS of the SMB server?*
- We search the 'script.db' file using grep to see what type of scripts contain 'smb' with the command ```grep "smb" script.db```
- We can see there is a file called "smb-os-discovery.nse", which is the answer.

![image22.jpg](attachment:image22.jpg)

- **Question 2:** *Read through this script. What does it depend on?*
- If we output the file, via ```cat smb-os-discovery.nse```, and we scroll down a bit, we can see that under 'dependencies', there is one, 'smb-brute', which is our answer.

![image23.png](attachment:image23.png)

![image24.jpg](attachment:image24.jpg)

![image25.png](attachment:image25.png)

## Task 13 - Firewall Evasion <a name="task13"></a>

<p> Your typical windows host with its default firewall will block all ICMP packets. We often use a 'ping' to manually establish the activity of a target  and Nmap does the same thing by default. This means that Nmap will register a host with this firewall configuration as dead and not bother scanning it at all.</p>
<p> Nmap has a way around this with the switch '-Pn', which tells Nmap to not bother pinging a host before scanning it. This means Nmap will always treat the target host(s) as being alive, effectively bypassing the ICMP block; however, it comes at the price of potentially taking a very long time to complete the scan (if the host really is dead then Nmap will still be checking and double checking every specified port). It's also worth noting that if you're already directly on the local network, Nmap can also use ARP requests to determine host activity.</p>

- Switches Nmap considers useful for firewall evasion can be found on it's website https://nmap.org/book/man-bypass-firewalls-ids.html
- Some good switches that can be used are:

- ```-f```: Used to fragment the packets (i.e. split them into smaller pieces) making it less likely that the packets will be detected by a firewall or IDS.
- An alternative to ```-f```, but providing more control over the size of the packets is ```--mtu <number>```, which accepts a maximum transmission unit size to use for the packets sent. This must be a multiple of 8.
- ```--scan-delay <time>ms```: used to add a delay between packets sent. This is very useful if the network is unstable, but also for evading any time-based firewall/IDS triggers which may be in place.
- ```--badsum```: this is used to generate in invalid checksum for packets. Any real TCP/IP stack would drop this packet, however, firewalls may potentially respond automatically, without bothering to check the checksum of the packet. This switch can be used to determine the presence of a firewall/IDS.


**End Questions**

- **Question 1:** *Which simple (and frequently relied upon) protocol is often blocked, requiring the use of the ```-Pn``` switch?*
- ICMP is the answer, which is in the text in the room.
- **Question 2:** *[Research] Which Nmap switch allows you to append an arbitrary length of random data to the end of packets?*
- Use the command ```nmap -h``` and scroll down to 'FIREWALL/IDS EVASION AND SPOOFING' seeing as this task is about 'Firewall Evasion'. There you will see the switch ```--data-length <num>``` which is our answer.

![image26.png](attachment:image26.png)

![image27.jpg](attachment:image27.jpg)

![image28.png](attachment:image28.png)

## Task 14 - Practical <a name="task14"></a>

- Now we will scan the target machine we deployed in Task 1.

**End Questions**

- **Question 1:** *Does the target (MACHINE_IP) respond to ICMP (ping) requests (Y/N)?*
- Ping your own TryHackMe machine ip address and wait a minute or 2. We see we get no response. We see in the ping statistics that 87 packets were transmitted but 0 was received with a 100% packet loss and hence the answer here is 'N'.

![image29.png](attachment:image29.png)

- **Question 2:** *Perform an Xmas scan on the first 999 ports of the target -- how many ports are shown to be open or filtered?*
- The Xmas scan switch is ```-sX```. We will scan ports 1 to 999. I like to use the switch ```-vv``` for extra verbosity so the result is easier to read. The first time I tried the Xmas scan, it did not work as I did not use the ```-Pn``` switch, which is a way around ICMP blocking as we know from Task 13. So, the correct command is ```nmap -sX -p1-999 -vv 10.10.132.50 -Pn```, which takes a bit longer to run. When the scan is done, all 999 ports are shown as ```open|filtered```.

![image30.png](attachment:image30.png)

- **Question 3:** *There is a reason given for this -- what is it? Note: The answer will be in your scan results. Think carefully about which switches to use -- and read the hint before asking for help!*
- We can see from the scan, the reason for the 999 ports being shown as open|filtered is because we get 'no-response' from them, and this is our answer.

- **Question 4:** *Perform a TCP SYN scan on the first 5000 ports of the target -- how many ports are shown to be open?*
- Here, we use a TCP SYN scan, which we first came across in Task 6. It is basically the same command as question 2. We use the ```-Pn``` switch again as by default Nmap only does it's port scans against hosts that are up and we want Nmap to perform it's fucntion as if the host is active. So the command is ```nmap -sS -p1-5000 -vv 10.10.132.50 -Pn``` where we see that 5 ports are open.

![image31.png](attachment:image31.png)

- **Question 5:** *Open Wireshark (see Cryillic's Wireshark Room for instructions) and perform a TCP Connect scan against port 80 on the target, monitoring the results. Make sure you understand what's going on.*
- No answer needed here that needs going through.

- **Question 6:** *Deploy the ```ftp-anon``` script against the box. Can Nmap login successfully to the FTP server on port 21? (Y/N)*
- We looked at scripts earlier so let's see if we can login successfully with the ftp-anon script. The command is ```nmap --script=ftp-anon -vv 10.10.132.50 -Pn``` where we see that 'anonymous FTP login allowed', so our answer is 'Y'

![image32.jpg](attachment:image32.jpg)

![image33.png](attachment:image33.png)

## Task 15 - Conclusion <a name="task15"></a>

- This walkthrough is now complete.
- Hopefully I have helped explain some of the solutions and concepts along the way.

**End Questions**
- Read the conclusion and click submit and you are done!

![image34.png](attachment:image34.png)
