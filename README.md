# pfSense Network Lab: Exposing Internal Web Server to External Network via Port Forwarding

This project aims to build an isolated Local Area Network (LAN) behind a pfSense firewall in a virtualized environment and safely forward (Port Forwarding / NAT) an HTML website hosted on an internal Windows 10 machine to the external network (WAN) via the pfSense WAN IP.

## 📺 Application Video
You can access my unlisted YouTube video, which contains the step-by-step implementation and testing phases of the project, via the link below:
👉 **[Click Here to Watch the Lab Application Video](https://www.youtube.com/playlist?list=PLya37EFE2qM2a8TTLQNu1XkME9VkrU5QH)**

---

## 🗺️ Network Topology
The system architecture is based on a dual-homed firewall structure where the internal network is completely isolated, and its communication with the outside world is provided exclusively through pfSense.

<img width="1247" height="397" alt="image" src="https://github.com/user-attachments/assets/52176ed5-705a-4b61-9659-9c79e45ae197" />


### Components and IP Configuration

| Device / Interface | Network Type | IP Address | Subnet Mask | Default Gateway (DG) |
| :--- | :--- | :--- | :--- | :--- |
| **Virtual Windows 10 (Web Server)** | Host-Only (LAN) | `192.168.10.10` | `255.255.255.0` | `192.168.10.1` |
| **pfSense LAN Interface** | Host-Only | `192.168.10.1` | `255.255.255.0` | *None* |
| **pfSense WAN Interface** | Bridge Mode | `192.168.1.10` | `255.255.255.0` | `192.168.1.1` |
| **Physical Host Computer** | Home Network | `192.168.1.106` | `255.255.255.0` | `192.168.1.1` |
| **Home Router** | WAN Gateway | `192.168.1.1` | `255.255.255.0` | ISP IP |

---

## 🛠️ Technical Details and Configuration Steps

### 1. Network Segmentation and Virtualization Settings
* The Windows 10 virtual machine and the first network card of pfSense were assigned to the same **Host-Only (or Virtual Switch)** network group to build the isolated internal network (LAN) on `192.168.10.0/24`.
* The second network card of pfSense was configured in **Bridge Mode** to obtain a WAN IP (`192.168.1.10`) from the physical home router (`192.168.1.0/24`).

### 2. Port Forwarding (Destination NAT / DNAT) Rules
A **Port Forwarding (Destination NAT)** rule was defined on pfSense so that a device on the external network (`192.168.1.0/24`) can access the internal web server when hitting the pfSense WAN IP:

* **Interface:** WAN
* **Protocol:** TCP
* **Source:** * (Any external source)
* **Destination:** WAN Address (HTTP port or a designated custom port)
* **Redirect Target IP:** `192.168.10.10` (Windows 10 Web Server)
* **Redirect Target Port:** HTTP (`8080`)

### 3. Firewall Rule Automation
When the Port Forwarding rule was defined on pfSense, the firewall rule allowing this traffic on the WAN interface was automatically generated. Thanks to this rule, external HTTP requests are successfully passed to the internal network without being blocked by the firewall.

---

## 🧪 Testing and Verification
1. A local web server hosting a simple HTML file (using IIS, Python HTTP Server, or similar) was launched on the internal Windows 10 machine.
2. A web browser was opened on the physical host computer (`192.168.1.106`) within the home network, and the pfSense WAN IP address `http://192.168.1.10` was visited.
3. pfSense successfully intercepted the incoming request, forwarded it to `192.168.10.10:8080` on the internal network, and verified that the HTML page loaded seamlessly from the external network.

## 🎯 Key Takeaways
Through this hands-on laboratory work, the following concepts were practiced:
* WAN and LAN interface management in firewall architectures,
* Destination NAT (DNAT) and Port Forwarding mechanics,
* Routing mechanisms over default gateways in isolated networks.
