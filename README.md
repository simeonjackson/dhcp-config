<h1>DHCP Administration & Configuration</h1>

<p>Following the deployment of a corporate Active Directory environment, this project focused on configuring a central DHCP server to automate IPv4 address allocation and manage network configurations across multiple subnets. </p>


<p>The objective was to design and deploy a multi-scope DHCP environment that accurately reflects a segmented corporate network (Users, Voice, and Guest Wi-Fi). This lab demonstrates core competency in IP Address Management (IPAM), preventing IP conflicts through exclusions, enforcing static assignments via MAC reservations, and centrally deploying critical network options (Default Gateway and DNS).</p>


<h2>Environments and Technologies Used</h2>

* Infrastructure: Windows Server 2022 Domain Controller (`dc01`)
* Management Tools: DHCP Management Console, Active Directory Domain Services (AD DS)
* Core Concepts: The DORA Process, Subnetting, Scope Options, Active Directory Authorization, MAC Reservations

<h2>The Setup</h2>

The lab involved installing the DHCP Server role, authorizing the server within the `lab.local` Active Directory domain to prevent rogue DHCP allocations, and configuring three distinct scopes across three subnets:

**1. Users LAN (192.168.1.0/24)**
* This subnet holds our standard employee workstations.


**2. Corporate Voice (192.168.2.0/24)**
* This subnet holds our VoIP phones and call manager appliances.


**3. Guest Wi-Fi (192.168.3.0/24)**
* This subnet holds our untrusted guest mobile devices and laptops.

Across all three subnets I also applied the same DHCP options. This was done to ensure clients could successfully route traffic and resolve internal hostnames. Getting these wrong could break the network so it is a good idea to deploy these options globally and per scope.
* **Option 003 (Router):** Configured as `.254` for each respective subnet to define the default gateway (e.g., `192.168.1.254`).
* **Option 006 (DNS Servers):** Pointed all clients to the `dc01` IP address for internal DNS resolution.
* **Option 015 (DNS Domain Name):** Configured as `lab.local` to ensure clients properly appended the corporate DNS suffix.



<h2>Configuration Steps</h2>
<p>
  First things first, I had to install and configure a DHCP Server on my Domain Controller.
</p>

<img width="1914" height="988" alt="Image" src="https://github.com/user-attachments/assets/22ed243a-7e6c-4969-b7de-cda8a97e147a" />

<img width="1912" height="982" alt="Image" src="https://github.com/user-attachments/assets/3568eecb-00c4-4a4e-8068-a533d9b882d5" />

<img width="1894" height="973" alt="Image" src="https://github.com/user-attachments/assets/e573c847-3ff0-46fd-94e2-32a172bcd0b5" />

<p>
  Once installed I opened up the DHCP Management Console which is where all the work in this lab was done.
</p>

<p>
  
  Under my domain, `lab.local`, I created my first scope under IPv4. This scope `192.168.1.10` to `192.168.1.200` is my LAN User scope which represents the standard network where all my everyday employees will connect.

<img width="1393" height="831" alt="Image" src="https://github.com/user-attachments/assets/3973aa55-56b9-47b0-bfcc-fc5b72f06f4b" />

<img width="1390" height="831" alt="Image" src="https://github.com/user-attachments/assets/518ce2a8-83a3-46d1-af34-4c62d46a11ef" />



  When making a scope there are many things to consider, in this lab I will focus on the following: 
  - IP address range (how many IP addresses can be leased out)
  - Exclusions (addresses that fall under the scope but are not distributed by the server)
  - Reservations (which use a devices MAC address to ensure that every time that devices is on the network it will have the same IP address)
  - Lease duration (determines how long a client can use a specific IP address from the scope)
  - DHCP options which I mentioned earlier which are "instructions the server gives to a client along with the IP address, like how to find the internet (Router/Gateway) and how to resolve website names (DNS).


Back to the LAN Scope, here are my notable configurations:

  **1. Users LAN (192.168.1.0/24)**
* **Purpose:** Standard employee workstations.
* **Scope:** `192.168.1.10` to `192.168.1.200` <img width="1393" height="826" alt="image" src="https://github.com/user-attachments/assets/981088dd-a9e3-4c12-ae44-e6a6cf4c59d0" />
* **Configuration:** 8-hour lease duration to accommodate daily office churn. <img width="1387" height="829" alt="Image" src="https://github.com/user-attachments/assets/7622efaf-4e4e-497a-a46f-a5d5b1a09aa8" />
* **Exclusions:** `192.168.1.10 - 192.168.1.20` reserved for statically addressed branch infrastructure (printers, switches). <img width="1389" height="832" alt="Image" src="https://github.com/user-attachments/assets/5213ef72-22d7-40e8-989f-6759fafde567" />
* **Reservations:** Created a MAC reservation for `Printer-01` at `192.168.1.15` to ensure consistent IP assignment while maintaining centralized DHCP management. <img width="996" height="573" alt="image" src="https://github.com/user-attachments/assets/bca79005-c34c-4ef9-b6b4-14923a36995e" /><img width="991" height="571" alt="image" src="https://github.com/user-attachments/assets/e717ca3e-c406-4dac-b328-e9033ac589ec" /><img width="996" height="570" alt="image" src="https://github.com/user-attachments/assets/0705b19f-2b9e-4ba7-843e-a4a6561fac71" />



**2. Corporate Voice (192.168.2.0/24)**
<img width="1488" height="847" alt="Image" src="https://github.com/user-attachments/assets/1a40f759-f19e-421b-9369-49c521e9d345" />
* **Purpose:** VoIP phones and call manager appliances.
* **Scope:** `192.168.2.10` to `192.168.2.150`<img width="1489" height="853" alt="Image" src="https://github.com/user-attachments/assets/f69f3a31-c0da-4b98-81b2-4d5a5d18abc8" />
* **Configuration:** 1-day lease duration for stable, static-location devices.<img width="1488" height="856" alt="Image" src="https://github.com/user-attachments/assets/daed3501-7690-4459-a1c2-41b2f8460b13" />
* **Exclusions:** `192.168.2.10 - 192.168.2.30` excluded for voice gateways.<img width="1492" height="856" alt="Image" src="https://github.com/user-attachments/assets/c7f425a1-2645-4c76-bdb3-a074c0d9eef5" />

**3. Guest Wi-Fi (192.168.3.0/24)**<img width="991" height="564" alt="Image" src="https://github.com/user-attachments/assets/a0ed3d8e-4488-4a48-a13a-0b17ba57f6f0" />
* **Purpose:** Untrusted guest mobile devices and laptops.
* **Scope:** `192.168.3.50` to `192.168.3.250`<img width="991" height="565" alt="Image" src="https://github.com/user-attachments/assets/5d6b0c77-3f91-4f9c-b697-4304d979d019" />
* **Configuration:** Aggressive 2-hour lease duration to rapidly reclaim IPs in a high-turnover wireless environment.<img width="991" height="570" alt="Image" src="https://github.com/user-attachments/assets/6130bbc7-4701-44c2-8b43-5e78930f5c39" />
* **Exclusions:** `192.168.3.50 - 192.168.3.60` reserved for wireless access points and captive portal appliances.<img width="991" height="567" alt="Image" src="https://github.com/user-attachments/assets/e629b665-8bde-490f-a35a-73499bffc1bd" />

</p>

<p>
  The last few tabs when creating a new scope are where you set the DHCP Options as well as activate the scope. These steps are the same for all three scopes I made so I will use Users LAN as an example to set options and activate.
</p>

Router (Default Gateway)
<img width="1392" height="832" alt="Image" src="https://github.com/user-attachments/assets/97ad2ec8-8c8b-4aa3-ace0-d6d3822fc464" />
Domain and DNS Servers
<img width="1395" height="835" alt="Image" src="https://github.com/user-attachments/assets/36bd5fbd-69f1-4676-a0e4-a08bf0dd2882" />
Activiating Scope
<img width="1392" height="826" alt="Image" src="https://github.com/user-attachments/assets/7797230c-b6d0-4b7b-b466-e6b00d8ec237" />

<img width="1390" height="832" alt="Image" src="https://github.com/user-attachments/assets/e9c82858-80d7-44a2-b84e-05a23fff2137" />

Also wanted to note that the Printer that I made a DHCP reservation for earlier will inherit the options from its scope.
<img width="993" height="563" alt="Image" src="https://github.com/user-attachments/assets/ce63a3df-f5ff-42a5-8d11-d11aa17c479b" />

So now we have all three of our scopes up and running providing central management of key infrastructure, managing static IP addresses through Reservations instead of static assignments and correct DNS and gateway options for each individual subnet to ensure network functionality.
<img width="1538" height="902" alt="Image" src="https://github.com/user-attachments/assets/0d92b090-e4e4-4178-be11-87d299539102" />

<img width="1536" height="907" alt="Image" src="https://github.com/user-attachments/assets/e79182ad-ee9d-45c1-a8ee-ac7ef62e88c7" />

<h2>Takeaways</h2>

From what I have learned, DHCP can solve many problems at scale for enterprise environments but it is important to get it right. It was fun for me to get hands-on with some networking topics such as subnetting, DNS and IPAM and apply them in a way that would be useful for a business. The idea of assigning IP addresses statically would be a nightmare and DHCP provides centralized management and reduces errors and security issues drastically.

It was nice to tie in some networking concepts I am learning with Active Directory, where I have been spending a lot of time doing many of my labs. Seeing the work gel together is increasing my scope of understanding and I am excited to keep going.
