# Automated-IP-Address-Management-and-DHCP-Server-Implementation
A hands-on networking lab demonstrating the implementation and analysis of a Dynamic Host Configuration Protocol (DHCP) server in Cisco Packet Tracer. This repository covers setting up a dedicated server, configuring IP address pools, and automating IP assignment for client devices through the DORA process.



## Overview

- In a small or enterprise network, manually assigning static IP addresses to every computer is inefficient and prone to IP conflict errors. To solve this, a Dynamic Host Configuration
-  Protocol (DHCP) server is used to automatically allocate IP addresses, subnet masks, default gateways, and DNS server details to client devices whenever they connect to the network.
- This lab demonstrates how to set up a dedicated Server in Cisco Packet Tracer, configure the DHCP service on it, connect client PCs through a network switch, and verify automatic IP
- assignment through the DORA process.


## Network Topology Overview

- <img width="1917" height="727" alt="Screenshot 2026-10-18 162206" src="https://github.com/user-attachments/assets/18e83625-e574-41ae-9b5c-da26af4ec555" />

Here is the complete network layout I built in Cisco Packet Tracer to test dynamic IP assignment. The design combines both wired Ethernet connections and a wireless segment under a single network umbrella, managed by a dedicated DHCP server.

At the core of the setup sits a 2960 series switch acting as the central connectivity hub. On the wired side, I connected a dedicated DHCP Server directly to the switch using a straight-through cable, alongside six desktop PCs (PC0 to PC5). Each PC connects to an individual FastEthernet port on the switch.

To handle the wireless end devices, I linked a generic Access Point to one of the switch ports. This Access Point acts as a transparent bridge, broadcasting the wireless signal across the local area. Four wireless laptops (Laptop0 through Laptop3) are connected to this Access Point via Wi-Fi modules.

Since the Access Point simply passes traffic back and forth to the switch, both wired PCs and wireless laptops sit on the exact same local network segment. When any device boots up—whether plugged in via cable or connected through Wi-Fi—it broadcasts a request back to the central server to claim its IP configuration automatically.

## Initial IP Addressing Scheme (Static Configuration Phase)

Before enabling the DHCP service, I manually assigned static IP addresses to all devices to establish baseline network connectivity and ensure each endpoint was reachable within the same local subnet (192.168.1.0/24).

# DHCP Server
<img width="1433" height="821" alt="Screenshot 2026-10-18 162732" src="https://github.com/user-attachments/assets/45eb0830-5a31-44fa-933f-200c71c25409" />

- (Server0): Configured with a static IP address of 192.168.1.100 and subnet mask 255.255.255.0. Assigning a fixed IP to the server is necessary so it can consistently process incoming
- client requests once the DHCP pool is configured.

# Wired Desktops 
- <img width="1531" height="782" alt="Screenshot 2026-10-18 162437" src="https://github.com/user-attachments/assets/790db1d3-cff3-4686-85dc-ed8d56a164bf" />

- PCs (PC0 to PC5): Assigned sequential static IP addresses ranging from 192.168.1.1 through 192.168.1.6. For instance, PC0 was set up with 192.168.1.1 as shown in the interface setup.

# Wireless Laptops
- <img width="1813" height="756" alt="Screenshot 2026-10-18 162555" src="https://github.com/user-attachments/assets/1aa3fd38-1858-427c-9295-7f7fdb84a3cb" />

- Laptop0 to Laptop3: Configured with IP addresses from 192.168.1.7 to 192.168.1.10. Laptop0 received 192.168.1.7 over its Wireless0 interface connected via the Access Point.

- This manual assignment verified that both the physical switch links and the wireless 802.11 association were functioning properly across the single broadcast domain before switching the -
- client devices over to dynamic DHCP provisioning

## Network Connectivity Verification Using Ping Command
- After setting up the IP configurations, I verified end-to-end communication across both wired and wireless segments using the ping utility from PC0’s Command Prompt


- <img width="852" height="718" alt="Screenshot 2026-10-18 162845" src="https://github.com/user-attachments/assets/5e00d88d-b965-4f68-8b31-9a88444acbc8" />

- First, I pinged another wired PC (192.168.1.2) to test internal Ethernet communication within the switch. All 4 packets returned successfully with zero packet loss and minimum response
- time (<1ms), confirming a solid local wired link.then i pinged all the wired pc's 

- Next, I tested cross-segment connectivity by pinging one of the wireless devices (192.168.1.8, Laptop1). The ping requests were successful with 0% packet loss and an average latency of
- 30ms. This confirms that data packets are properly routing through the switch, passing through the Access Point, and reaching the wireless clients without any configuration issues.

## Testing Connection from Server to Clients (Ping Test)
- In this step, I used the Command Prompt on Server0 to test if the central DHCP server could communicate with the other devices on the network.

- <img width="1416" height="752" alt="Screenshot 2026-10-18 162950" src="https://github.com/user-attachments/assets/726873bd-0acc-470f-a8de-ae40afa97643" />

- Testing Wired Connection: I first pinged one of the wired desktops (192.168.1.1). All 4 packets were sent and received back with 0% packet loss and very fast response times (<1ms to
- 10ms).
- Testing Wireless Connection: Next, I pinged a wireless laptop (192.168.1.8). Again, the test was successful with 0% packet loss and an average latency of 23ms.

- This confirms that our central server is fully connected to both the wired switch network and the wireless access point, and all devices can seamlessly talk to each other without any
- packet drops.

  ## Configuring DHCP Service on the Server

  - To enable dynamic IP assignment across the entire network, I configured the DHCP service directly on Server0
 
- <img width="728" height="707" alt="Screenshot 2026-10-18 163114" src="https://github.com/user-attachments/assets/345032d9-9341-4f3f-a6d0-7e54a361db68" />
## step 1 
- Enabling the Service: Turned the DHCP Service status from Off to On so the server can active process client requests.
## step 2
- select the ip range 
- I set the starting IP address to .10 to leave the lower IP addresses (192.168.1.1 to 192.168.1.9) reserved for static devices. In our network, servers, switches, and access points
- require fixed IP addresses so they remain easily accessible at all times
## step 3
# Maximum Users Limit is Set to 25
- This parameter restricts the pool so the server will only assign IPs from 192.168.1.10 up to 192.168.1.34. Since our current network has 10 client devices (6 PCs and 4 Laptops), a limit - of 25 provides more than enough room for existing machines and future additions while preventing unauthorized devices from consuming unlimited IPs.

## save the configrations 
- Clicking Save updates the active server table (serverPool) and turns the service live. From this moment, the server actively listens on the local broadcast domain.
- Whenever a PC or Laptop is set to DHCP, it sends a request and automatically receives its IP address, subnet mask, and network settings within seconds without any manual entry.

## Verifying Dynamic IP Allocation on Client PCs

- After configuring and turning on the DHCP service on Server, I updated the IP configuration on the client endpoints to test dynamic address assignment.
# Switching to DHCP:- Opened the IP Configuration settings on PC0 and selected the DHCP radio button instead of Static

- Automatic Provisioning: Immediately upon switching, PC0 sent a broadcast request across the switch. The server responded and automatically assigned an IPv4 address of 192.168.1.11 along
- with the subnet mask 255.255.255.0.
- The window displayed the message "DHCP request successful," confirming that the client successfully obtained its IP network parameters dynamically from the server pool without any manual 
- input.

# Completing DHCP Configuration Across All End Devices
- After confirming dynamic IP allocation on the first PC, I applied the same DHCP settings to all remaining wired desktops (PC1 through PC5) and wireless laptops (Laptop0 through Laptop3).

## Final Network Verification and Active Packet Simulation

- <img width="1163" height="692" alt="Screenshot 2026-10-18 163457" src="https://github.com/user-attachments/assets/73056f97-2b96-4252-a163-7016358f9955" />
- This final layout demonstrates the fully operational network running in simulation mode, showcasing real-time data flow and active communication across all endpoints.

## Conclusion
- This project successfully demonstrates the design, setup, and verification of a hybrid local area network (LAN) combining both wired and wireless infrastructure using Cisco Packet
- Tracer. By configuring a dedicated DHCP server on Server0, the network was efficiently transitioned from static IP configuration to dynamic IP allocation (192.168.1.10/24 pool).   End-to-- end connectivity was verified through systematic ping tests and active Packet Tracer simulation mode, confirming 0% packet loss across all desktop PCs and wireless laptops. The
- implementation highlights the practical advantages of DHCP in reducing manual configuration errors, preventing IP conflicts, and simplifying scalable network management.
