<h1> Simulating Local Area Network Attacks and Mitigation Techniques  </h1>

<h2>Summary</h2>
In this paper we provide a prototype for modeling network computing attacks at <b>layer '2' of the OSI model</b>. Our goal is to replicate the most common attacks of this level while remaining realistic from an attacker's perspective. The simulator is built on a model of 3 - 4 computers on a shared network connected to a Cisco 2950 Catalyst switch. The work is based on the investigation of attacks that take place in the real world. We show how to perceive attacker vulnerabilities and exploits as well as vulnerability metrics on a Cisco Catalyst 2950 switch. In this paper, the present state of the data network is detailed in terms of both logical and physical topology and represented with the help of the <b>Cisco Packet Tracer</b> program. Finally, the attacks <b>ARP Spoofing/ARP Poisoning, MAC Address Table Flooding Attack, DHCP starvation and DHCP spoofing Attack, VLAN Hopping, Double-Tagging Attack Spanning Tree Protocol attack </b> are described in detail as well as the mitigations of these attacks set specific variables on the Cisco switch 2950 Catalyst.
<br>

<h2>Physical topology of the Network</h2>
Devices used:
<br>
<ul>
  <li>We have two hosts that will play the role of the user (UserPC) and the FileServer (FileServer).</li>
  <li>We have a laptop that will play the role of the attacker.</li>
  <li>A Switch Catalyst 2950 for configuration and mitigation of attacks.</li>
  <li>An Internet Home Router.</li>
</ul>  

<h2>Layer 2 Attacks and Mitigation Techniques</h2>
<h3>DHCP Starvation/Spoofing Attack and Mitigation</h3>
<b>Dynamic Host Configuration Protocol (DHCP)</b> is a network management protocol used in Internet Protocol (IP) networks to automatically assign IP addresses and other communication parameters to devices connected to the network using a client-server architecture. An overview of the DHCP message exchange between client and server is shown in the figure:

![270714034-40fca244-0ee3-4aa3-9a82-8310592f5236](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/4c682146-3605-4cc8-864d-b0b9f3449d16)

<h3>DHCP Attacks</h3>
Two types of DHCP attacks are <b>DHCP starvation and DHCPspoofing</b>. Both attacks are mitigated by implementing <b>DHCP snooping</b>. 
<br>
<b>DHCP Starvation Attack</b>, creates a DoS (Denial of Service) for connecting clients. DHCP Starvation Attacks require an attack tool such as <b>Yersinia</b>. <b>Yersinia</b> - is a network security/hacking tool for Unix-like operating systems designed to exploit a weakness in different network protocols.
<br>
<b>DHCP Spoofing Attack</b>, through a rogue DHCP server connected to the network, provides false IP configuration parameters to clients. A rogue DHCP server can provide a variety of misleading information, including:
<br>
<ul>
  <li>Wrong default gateway – The rogue Server provides an invalid gateway or host IP address to create a man-in-the-middle attack. This can go completely unnoticed as the attacker intercepts the flow of data through the network.</li>
  <li>Wrong DNS server - The rogue Server provides an incorrect DNS Server address that leads the user to a malware website.</li>
  <li>Wrong IP address - The rogue Server provides an invalid IP address which effectively creates a DoS attack on the DHCP client.</li>
</ul>  

<h3>The DHCP Starvation Attack and the Yersinia Tool</h3>
We will use Yersinia tool to perform DHCP Starvation Attack. This tool sends fake DHCP requests in order to exhaust all available IP addresses that can be assigned by the DHCP Server. During this attack, legitimate network users may be denied service.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/d34f2c67-1b06-4e28-b1d2-4fe2c4be709b)

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/0663f086-724e-4b26-97d1-ab3f2fae6a03)

After using Yersinia, we go to the Switch to see what happens when the <b>DHCP Starvation Attack</b> takes place. By running the <b>show ip dhcp pool</b> command in the command line of the switch, we display the IP address allocations and related usages.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/0ae3bca2-cce2-4ead-9da0-9b845d4e1612)

<h3>DHCP Spoofing Attack and the Ettercap tool</h3>
We will use <b>Ettercap</b> tool to perform <b>DHCP Spoofing Attack</b>. Then we give the IP address pool, since the gateway is 192.168.1.1, then we give 192.168.1.5-100 and so it will attack the entire subnet of the gateway. Then we set the DNS to 192.168.1.21 (Attacker IP) and start the attack.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/f528950f-b3d6-4f0f-86b5-e84a3005c7c5)

When the attack is finished, we go to the User's computer, in PoweShell to execute the commands ipconfig /release and ipconfig /renew. We notice that after the command ipconfig /renew the host computer takes as Default Gateway the IP address of the attacker, in this case we have <b>DHCP Spoofing Attack: Wrong default gateway</b>.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/23f2b116-5c56-41f5-899b-2ac9595e5448)


<h3>Mitigation of DHCP Attacks</h3>
<b>DHCP Attacks</b> can be effectively mitigated using the switch's <b>port-security</b> because the Yersinia tool uses a unique source MAC address for each DHCP request sent. However, mitigation of DHCP Starvation Attacks requires more protection. DHCP Attacks can be mitigated by using DHCP Snooping on trusted ports.
<br>
<br>
DHCP Snooping filters the DHCP messages and limits DHCP traffic to untrusted ports.
<ul>
  <li>Devices under administrative control (eg switches, routers, and servers) are trusted sources.</li>
  <li>Trusted interfaces (eg trunk links, server ports) must be explicitly configured as trusted.</li>
  <li>Off-network devices and all access ports are generally treated as untrusted sources.</li>
</ul>  
A DHCP table is created that includes the source MAC address of a device on an untrusted port and the IP address assigned by the DHCP Server to that device.
<ul>
  <li>MAC address and IP address are linked together.</li>
  <li>This table is called DHCP snooping binding table.</li>
</ul> 
We follow the following steps when configuring the switch to enable DHCP Snooping:
<ul>
  <li><b>Step 1</b>. Enable DHCP Snooping using the global configuration command ip dhcp snooping.</li>
  <li><b>Step 2</b>. On trusted ports, use the ip dhcp snooping trust command.</li>
  <li><b>Step 3</b>: On untrusted interfaces, we limit the number of DHCPdiscovery messages that can be received using the ip dhcp snooping limit rate packets-per-second command.</li>
  <li><b>Step 4</b>. Enable DHCP Snooping by VLAN or by a range of VLANs, using the ip dhcp snooping vlan command.</li>
</ul> 

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/b49257c2-7ed4-4188-a2a2-819961929d2d)

![270714034-40fca244-0ee3-4aa3-9a82-8310592f5236](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/e8d22ae6-27bf-48bf-a5c2-f8526fea03c2)

Since we have completed the Mitigation process with Port security on the Switch, we use the show ip dhcp snooping command to verify the DHCP Snooping settings. Next, we use the show ip dhcp snooping binding command to see which clients have received DHCP information.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/4e3fb1cf-a994-4fb4-90d3-1ba4b56095cd)

<h3>ARP Spoofing/ARP Poisoning Attack and Mitigation</h3>
<h3>The Address Resolution Protocol (ARP)</h3>
<b>Address Resolution Protocol (ARP)</b> is a protocol that allows network messages to reach a specific network device. ARP is often used by devices to communicate with the router or gateway that allows them to connect to the Internet.
Hosts maintain an ARP cache, which is a mapping table between IP addresses and MAC addresses, and use it to connect to network destinations. If the host does not know the MAC address for a particular IP address, it sends an ARP request packet to other machines on the network, asking for the corresponding MAC address.
<br>
Because the ARP protocol is not designed for security, it does not verify that an ARP response originates from an authorized party. It also allows hosts to receive ARP replies even if they have never made a request. This is a flaw in the ARP protocol that allows <b>ARP Spoofing/ARP Poisoning attacks</b>.
<br>
In the older IPv4 standard, ARP only works with 32-bit IP addresses. The newer IPv6 protocol, <b>Neighbor Discovery Protocol (NDP)</b>b>, is more secure and uses cryptographic keys to authenticate host identities. However, because most of the Internet still uses the older IPv4 protocol, ARP is still widely used.
<br>
Before performing <b>ARP Spoofing/ARP Poisoning</b>b>, we run arp–a command on both hosts. The arp command displays and edits the address translation tables.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/c6adbd81-36a4-47cc-b2ad-a83ebcadb653)

ARP table for the user (UserPC) before the ARP attack

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/6650a0fd-a5ca-4b67-9eac-824dafaab798)

ARP table for the FileServer before the ARP attack
