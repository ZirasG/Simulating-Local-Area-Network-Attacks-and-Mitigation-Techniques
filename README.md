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
In the older IPv4 standard, ARP only works with 32-bit IP addresses. The newer IPv6 protocol, <b>Neighbor Discovery Protocol (NDP)</b>, is more secure and uses cryptographic keys to authenticate host identities. However, because most of the Internet still uses the older IPv4 protocol, ARP is still widely used.
<br>
Before performing <b>ARP Spoofing/ARP Poisoning</b>, we run arp–a command on both hosts. The arp command displays and edits the address translation tables.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/c6adbd81-36a4-47cc-b2ad-a83ebcadb653)

ARP table for the user (UserPC) before the ARP attack

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/6650a0fd-a5ca-4b67-9eac-824dafaab798)

ARP table for the FileServer before the ARP attack

<h3>ARP Spoofing/ARP Poisoning Attack and the Ettercap tool</h3>
ARP Spoofing/ARP Poisoning is a <b>Man in the Middle (MitM)</b> attack that allows attackers to interfere with the communication of network devices. The attack is performed as follows:
<br>
<ul>
  <li>The attacker must have access to the network. They scan the network for the IP addresses of at least two devices, say a workstation (UserPC) and a FileServer.</li>
  <li>The attacker sends fake ARP replies using a spoofing tool such as Arpspoof or Ettercap.</li>
  <li>ARP Spoofing/ARP Poisoning advertises that the correct MAC address for both the user's IP address (UserPC) and its IP address (FileServer) is the attacker's MAC address. This causes both the user (UserPC) and (FileServer) to connect to the attacker's machine and not to each other.</li>
  <li>The two devices change their ARP cache entries and from there communicate with the attacker and not with each other.</li>
  <li>The attacker silently monitors all communications.</li>
</ul> 
<br>
In this case we will use the Ettercap tool.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/6bd0f725-8251-443c-91de-2f90039830fe)

Since we sent fake ARP replies using Ettercap. ARPSpoofing/ARPPoisoning advertises that the correct MAC address for both the FileServer IP address and the UserPC IP address is the Attacker's MAC address. The two devices change their ARP cache entries and from then on communicate with the attacker and not with each other.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/bea40faa-85e2-484a-a74f-25e881883253)

Then we run <b>arp –a </b>command on both hosts to see if ARP Spoofing/ARP Poisoning was done successfully.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/04da9020-70ff-446c-98b6-1315f79234ff)

ARP table for the user (UserPC) after the ARP attack

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/13a22597-f92c-4bad-a5f3-48c326c7027c)

ARP table for the FileServer after the ARP attack

We notice that the ARP Spoofing/ARP Poisoning has been successfully performed because the MAC Address of both the user (UserPC) and the file server (FileServer) point to the attacker's host, so when these two want to communicate they will do so through the intruder, who will silently monitor all communications.

![270956219-2361c4b7-6c61-4bd5-acde-7f3dd02ed766](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/d3c1c464-7ba6-489b-a550-ad7fef9ead66)

As the MAC addresses in the ARP tables of the two devices have changed, they now communicate through the attacker and not each other. As shown in the picture above.

<h3>The Mitigation of ARP Attacks</h3>
A switch must ensure that only valid ARP requests and responses are delivered. So to prevent <b>ARP Spoofing/ARP Poisoning</b> we must configure the switch:
<ul>
  <li><b>Dynamic ARP inspection (DAI)</b> requires DHCP Snooping and helps prevent ARP attacks by not relaying invalid or useless ARP replies to other ports on the same VLAN.</li>
  <li>All ARP requests and replies to untrusted ports are blocked.</li>
  <li>Each intercept packet is checked for a valid IP-to-MAC connection.</li>
  <li>Discard and log incorrect ARP replies to prevent ARP poisoning.</li>
  <li>If the predefined DAI number of ARP packets is exceeded, the interface is disabled.</li>
</ul> 
We follow these DAI configuration suggestions to reduce the possibility of ARP Spoofing/ARP Poisoning:
<ul>
  <li>We enable DHCP Snooping globally.</li>
  <li>Enable DHCP Snooping on a subset of VLANs.</li>
  <li>We enable DAI on specific VLANs.</li>
  <li>We configure trusted interfaces for DHCP Snooping and ARP Inspection.</li>
</ul> 
It is generally recommended to configure all access switch ports as untrusted and all uplink ports connected to other switches as trusted.
Switch1 in the previous topology connects two users (User PC and FileServer) to VLAN 10.
<ul>
  <li>DAI will be created to deal with ARP Spoofing/ARP Poisoning.</li>
  <li>DAI requires the DHCP Snooping binding table to work, so it is enabled.</li>
  <li>Then, for computers in VLAN10, DHCP Snooping and ARP Inspection are enabled.</li>
  <li>Because the uplink port of the router is trusted, it is configured as trusted for DHCP Snooping and ARP Inspection.</li>
</ul> 

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/16b60fd2-58a2-4dbe-bb49-dea06dbf87ea)

DAI can also be configured to check destination or source MAC and IP addresses:
<ul>
  <li><b>Destination MAC</b> - Checks the destination MAC address in the Ethernet header against the target MAC address in the ARP body.</li>
  <li><b>Source MAC</b> - Checks the source MAC address in the Ethernet header against the sender's MAC address in the ARP body.</li>
  <li><b>IP address</b> - Checks the ARP body for invalid and unexpected IP addresses, including 0.0.0.0, 255.255.255.255, and all multicast IP addresses.</li>
</ul> 
The <b>ip arp inspection validate {[src-mac][dst-mac][ip]}global configuration</b> command is used to configure DAI to drop ARP packets when IP addresses are invalid. It is useful when the MAC addresses in the ARP packet body do not match the addresses provided in the Ethernet header. <b>Only one command can be specified as in the following example. As a result, multiple ip arp inspection validate commands will replace the previous command</b>. We enter more than one validation method on the same command line, as shown in the output.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/017a8558-067e-4e4b-8942-bb410d2c1e9e)

<h3>MAC Address Table Flooding Attack and Mitigation</h3>
<h3>MAC Address</h3>
A <b>MAC address</b>, also known as a “hardware address” or “physical address”, is a binary number used to uniquely identify network adapters. Packets sent over Ethernet always originate from a MAC address and are sent to a MAC address. If a network adapter receives a packet, it compares the packet's destination MAC address with the adapter's own MAC address. If the addresses match, the packet is processed, otherwise it is dropped. Traditional MAC addresses are 12-digit hexadecimal numbers. The left six hexadecimal digits of the address correspond to a manufacturer's unique identifier, while the right six correspond to the serial number of the network interface card (NIC).

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/dc19d802-f791-4ac4-9a12-6308cb70dcd8)

<h3>Overview of Operation of a Layer 2 Switch</h3>
In making forwarding decisions, a LAN Layer 2 switch builds a table based on the source MAC addresses in the received frames. This is called the MAC address table. MAC address tables are stored in memory and used for more efficient frame switching.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/1db3607f-7df2-4292-9270-37505581f07d)

All MAC tables have a fixed size so a switch can run out of resources in which to store MAC addresses.
<h3>MAC Address Table Attack/Table Flooding Attack</h3>
<b>MAC Address Table Flooding</b> attacks exploit this limitation by bombarding the switch with fake source MAC addresses until the switch's MAC address table is full.
<br>
The switch then treats the frame as unknown unicast and starts flooding all incoming traffic from all ports on the same VLAN without referring to the MAC table. This condition allows an attacker to capture all frames sent from one host to another on the local LAN or local VLAN.
<h3>Use of the Macof tool</h3>
<b>Macof</b> is a tool that an attacker can create a <b>MAC Address Table Flooding attack</b>. For example, a Catalyst 6500 switch can store 132,000 MAC addresses in its MAC address table. A tool like macof can flood a switch with 8,000 fake MAC Addresses per second.
Another reason these attack tools are dangerous is because they not only affect the local switch, but they can also affect other layer 2 local switches. When a switch's MAC address table is full, it starts to flood all ports, including those connected to other layer 2 switches.
<br>
<b>Macof</b> is a member of the Dsniff suite of tools and is mainly used to flood the switch on a local network with MAC addresses. The reason for this is that the switch regulates the flow of data between its ports.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/a62a7de5-9aed-40cc-abb0-f7fe51594024)

After using the Macof tool, which flooded the switch with MAC addresses, we go to the switch to check the MAC Address Table:

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/a52c735b-9c32-4918-a67f-c04b1e41eca0)

We notice that the MAC Address Table of the switch is full so it treats incoming frames as unknown unicast and starts flooding all incoming traffic from all ports on the same VLAN without referring to the MAC table.

![270961640-ff6d558a-3f32-4a88-a9e5-f3db5881da15](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/4c71e794-d890-42f1-9ea6-40609845cd0d)

In the picture all incoming frames (1,2) to the switch sent from either User (1) or FileServer (2) are treated as unknown unicast, so the switch starts flooding all incoming traffic from all ports to all the ports without referring to the MAC table.

<h3>The MAC Address Table Attack Mitigation</h3>
To mitigate MAC Address Table Flooding attacks, network administrators must implement <b>port security</b>. <b>Port security</b> will only allow a specified number of MAC addresses on each port.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/ed90c272-7e17-4236-a580-314caebf6bb4)

The configuration port security violation of the switch.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/d0b5461f-19d6-40ac-91b5-aafd99812cf6)

The configuration port security mac-address sticky.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/a35b9885-0bd0-442f-89b1-d183015e9196)

We check the port-security for each interface (fa0/1, fa0/2).
<br>
Since the switch configuration process has been completed, we repeat the MAC Address Table Flooding attack with the macof tool and observe the switch logs.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/7973f0be-475a-4439-85c5-37c4a487ef58)

We notice that when a MAC address is entered in the switch table, through the macof tool, different from those given in the port-security configuration of the switch for each interface, the switch displays error detected messages for each violation that occurred, for each interface.
<br>
Next we need to determine what caused the security breach. If an unauthorized device connects to a secure port, we must first eliminate the threat and then re-enable the port.
<br>
To re-enable the port, first use the <b>shutdown</b> command and then we use the <b>noshutdown</b> command.

<h3>VLAN Double-tagging/Hopping Attack and Mitigation</h3>
<h3>VLANs</h3>
A <b>virtual LAN (VLAN)</b> is a network that groups a subset of devices that share a physical LAN, isolating traffic for each group. A LAN is a group of computers or other devices in the same space that share the same physical network. For example User and FileServer are in different VLAN.

<h3>The VLAN Double-tagging/Hopping Attack</h3>
A <b>VLAN Hopping Attack</b> allows traffic from one VLAN to be seen by another VLAN without the help of a router. In a VLAN Hopping Attack, the attacker configures a host to act as a switch to take advantage of the <b>automatic port trunking</b> feature enabled on most switch ports.
<br>
The attacker configures a host to <b>spoof 802.1Q signaling and Dynamic Trunking Protocol (DTP) </b> signaling on the switch. If successful, the switch establishes a connection to the host. Now the attacker can access all VLANs of the switch. The attacker can send and receive traffic on any VLAN, effectively hopping between VLANs.

![270965162-3589ce42-e6ef-4a5c-8dfa-9a24496a4814](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/85cd7328-984e-45a6-bac2-b58a10db7d3b)

The attacker can embed a hidden 802.1Q tag inside the frame that already has a hidden 802.1Q tag. This tag allows the frame to switch to a VLAN that the original 802.1Q tag did not specify.
<ul>
  <li><b>Step 1</b>: The attacker sends a double-tagged 802.1Q frame to the switch. The outer header has the VLAN tag of the attacker, which is the same as the VLAN of the trunk port.</li>
  <li><b>Step 2</b>: The frame arrives at the first switch, which looks at the first 802.1Q tag. The switch sees that the frame is destined for the VLAN. The switch forwards the packet from all VLAN ports after removing the first VLAN tag. At this point, the inner VLAN tag (second tag) is still intact and has not been checked by the first switch.</li>
  <li><b>Step 3</b>: The frame arrives at the second switch which does not know it was for the previous VLAN. The second switch only looks at the 802.1Q inner tag (second tag) inserted by the attacker and sees that the frame is intended for the target VLAN. The second switch sends the frame to the target, depending on whether there is an existing entry in the MAC address table for the target.</li>
</ul> 
A <b>VLAN double-tagging/hopping</b> attack is unidirectional and only works when the attacker is connected to a port that is in the same VLAN as the trunk port's native VLAN. VLAN double-tagging/hopping allows an attacker to send data to hosts or servers on a VLAN that would otherwise be blocked by an access control list configuration. Return traffic will also be allowed, thus allowing an attacker to communicate with devices on the blocked VLAN.

We check that the devices on VLAN 10 (FileServer & Attacker) are pinging.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/a611674d-2547-45d5-9f16-ca16738d3ab2)

Ping from FileServer (192.168.10.6) to attacker (192.168.10.21)

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/d546c33a-4d29-410a-b6a1-6dc0914c79a6)

Ping from attacker (192.168.10.21) to FileServer (192.168.10.6)

We check if the devices from VLAN 10 to VLAN 11 (Attacker&User) are communicating and notice that the two devices cannot communicate. We get a "Destination Host Unreachable" message from the default gateway.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/11326947-b43f-4b9e-9c77-7ce698ee0cdc)

Ping from attacker (192.168.10.21) to User (192.168.11.5) before the attack

We will use the <b>Yersinia</b> tool to perform VLAN double-tagging/hopping on the LAN. For the attack to be successful, the switch mode must be set to dynamic desirable, dynamic auto, or trunk so that the switches can negotiate and send DTP packets. By default, Cisco switches are set to dynamic desirable. 
<br>
First, we go to the yersinia tool under the DTP (Dynamic Trunk Protocol) tab and select “enabling trunking” and then OK.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/53125199-a444-4984-8eda-965e4966d952)

Next, we go to the 802.1Q tab and select “sending 802.1Q double enc.packet”.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/cbce0924-8e09-4501-b077-4ff35923418b)

After the attack is completed, we check if the devices from VLAN 10 to VLAN 11 (Attacker&User) can now communicate and observe that the two devices can communicate.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/8d8f7790-39b5-4b24-969d-522c7798ef5d)

Ping from attacker (192.168.10.21) to User (192.168.11.5) before the attack

<h3>The Mitigation of VLAN double-tagging/hopping Attack</h3>
VLAN double-tagging/hopping attacks can be prevented by implementing the following trunk security guidelines:
<ul>
  <li>We disable trunking on all access ports.</li>
  <li>We disable auto trunking on trunk links, so that trunks must be activated manually.</li>
  <li>We make sure that the native VLAN is only used for trunk links.</li>
</ul> 

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/8902b0e2-abc7-4fbe-8615-8f2a8a00af56)

<h3>The Spanning Tree Protocol (STP) Attacks and Mitigation</h3>
<h3>The Spanning Tree protocol</h3>
<b>Spanning Tree Protocol (STP)</b> is a network protocol that creates a logical loop-free topology for Ethernet networks. The main function of STP is to prevent loops. Spanning tree also allows a network topology to include backup interfaces that provide fault tolerance should an active interface fail.
<br>
STP creates a spanning tree that characterizes the relationship of nodes within a network of layer-2 connected bridges and disables those links that are not part of the spanning tree, leaving a single active path between any two network nodes.
That is:
<ul>
  <li>STP is used in switches to enable redundancy in the topology.</li>
  <li>It is done to block unwanted links/ports</li>
  <li>By default, switches run STP, no configuration needed</li>
  <li>STP Uses an algorithm called Spanning Tree Algorithm (STA) to select the port to terminate.</li>
  <li>STA also decides some other option using STP</li>
</ul> 
We decide which unwanted ports should be in blocking state to prevent a loop in the topology, this means we choose Unassigned/Backup ports in the topology.

<h3>The Spanning Tree Protocol (STP) Attack and the Yersinia Tool</h3>
Network attackers can manipulate the <b>Spanning Tree Protocol (STP)</b> to perform an attack by spoofing the bridge root and changing the topology of a network. Attackers can then capture all network traffic.
<br>
To conduct an STP attack, the attacking host broadcasts STP <b>Bridge Protocol Data Units (BPDUs)</b> containing configuration and topology changes that will force spanning-tree calculations. BPDUs sent by the attacking host announce a lower bridge priority in an attempt to be elected as the bridge root.
<br>
We have the network topology, with 2 switches, before the attack as shown in figure switch1 is originally the bridge root switch, which is a special bridge at the top of the Spanning Tree. The Ethernet connections then branch out from the root switch, connecting to other switches on the local area network (LAN).

![270968771-eb53db4e-e643-46d7-86bc-730b8ac14829](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/92391a94-2869-45c9-9f67-7774e4a50013)

Before using the yersinia tool, we check the configuration of the 2 switches to see which is the bridge root switch.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/19bbccc0-0be3-40df-872e-04508c16b365)

Switch - 1

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/26ea49f5-79ea-4337-8688-7fdfb60032d5)

Switch - 2
<br>
<br>
After we have checked both switches, we start the STP Attack process with the yersinia tool

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/70e0c4ef-f8e7-4dad-a135-ea2ce70ddba3)

We select in the tool "claiming root role" so that our terminal becomes the bridge root switch and the network traffic passes through it.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/8939e654-eaf6-4c05-8dd8-8eba3ca88b8f)

Then we notice that the bridge root switch has been changed. Checking switch1 we see that it is no longer a bridge root switch.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/86b54f1e-2d88-40c9-ba1e-6af8dcb1005a)


<h3>The Mitigation of Spanning Tree Protocol (STP) Attack</h3>
For mitigation of STP attacks, we use <b>PortFast and Bridge Protocol Data Unit (BPDU) Guard</b>. <b>PortFast</b> immediately brings a port into the forwarding state from a blocking state, bypassing the listening and learning states. We implement PortFast on all end-user access ports. In our case we apply PortFast to interface fa0/0.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/5dea2b92-bf6e-4b1c-9211-42bfe63a0cc1)

<b>PortFast</b> bypasses STP listening and learning states to minimize the time access ports must wait for STP to converge. PortFast on links between switches can create a spanning-tree loop.
PortFast can be enabled:
<ul>
  <li>On an interface (Interface) – Using the spanning-tree portfast command.</li>
  <li>Globally – Using the <b>spanning-tree portfast normal default</b> global configuration command.</li>
</ul> 
To verify if PortFast is enabled, we can use the commands:
<ul>
  <li><b>show running-config | begin span</b></li>
  <li><b>show spanning-tree summary</b></li>
</ul>

<b>BPDU guard </b> immediately disables a port that receives BPDUs. Like PortFast, BPDU should only be configured on interfaces connected to end devices.

![image](https://github.com/ZirasG/Simulating-Local-Area-Network-Attacks-and-Mitigation-Techniques/assets/145548499/94e7f24f-84f1-49e9-8ef6-1f2cdfa0c7ca)

An access port could receive BPDUs by mistake or because a user connected an unauthorized switch to the access port. If a BPDU is received on an access port with BPDU Guard capability, the port is placed in an error-disabled state. This means that the port has been closed and must be manually re-enabled or automatically recovered via the errdisable recovery cause psecure_violation command.
<br>
BPDU Guard can be enabled:
<ul>
  <li>On an interface (Interface) – Using the spanning-tree bpduguard enable command.</li>
  <li>Globally – Using the spanning-tree portfast bpduguard default global configuration command on all access ports.</li>
</ul>

<h2>CONCLUSIONS</h2>
In this project, we focused on data link layer (Layer 2) security issues in IP over Ethernet networks. We introduced the security concerns at Layer 2 and switchLayer 2 specifically on a Ciscoswitch 2950 Catalyst and summarized some of the possible attacks at this layer. Additionally, we provide an overview of the attacks, methods, and tools that can be used for local area networks (LAN/s). Finally, as an initial approach to improving LAN security, we proposed a set of threat mitigation rules and guidelines that we used at Layer 2 during frame transmission and through the Switch CLI (Console). Future work should focus on automating switches to include these mitigation rules without manual configuration. Finally, the IP and MAC address association problem should be studied and addressed in general.






