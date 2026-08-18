Networking Fundamentals – DNS, IP, CIDR & Ports
Task 1: DNS – How Names Become IPs
1. What happens when you type google.com in a browser?

When I type google.com, the browser first needs to find the IP address associated with the domain name. It sends a DNS query to a DNS resolver, which finds the required DNS record. The browser then connects to the returned IP address using TCP/HTTPS and sends the web request. The server responds with the website content.

2. DNS Record Types
Record	Meaning
A	Maps a domain name to an IPv4 address.
AAAA	Maps a domain name to an IPv6 address.
CNAME	Creates an alias from one domain name to another domain name.
MX	Specifies the mail servers responsible for receiving email for a domain.
NS	Identifies the authoritative name servers for a domain.
3. Run dig google.com
dig google.com

Look for the ANSWER SECTION:

;; ANSWER SECTION:
google.com.    <TTL>    IN    A    <IPv4-address>

A record: PASTE YOUR ACTUAL IP HERE
TTL: PASTE YOUR ACTUAL TTL HERE

TTL means Time To Live. It tells DNS resolvers how long the DNS response can be cached.

Task 2: IP Addressing
1. What is an IPv4 address?

An IPv4 address is a 32-bit address used to identify a device/interface on an IP network.

It is normally written as four decimal numbers separated by dots.

Example:

192.168.1.10

Each part is called an octet and can have a value from 0 to 255.

2. Public vs Private IP

Private IP: Used inside local/private networks and is not directly routable on the public Internet.

Example:

192.168.1.10

Public IP: An Internet-routable address assigned for communication over the public Internet.

Example:

8.8.8.8
3. Private IPv4 Ranges
Range	Private Range
10/8	10.0.0.0 – 10.255.255.255
172.16/12	172.16.0.0 – 172.31.255.255
192.168/16	192.168.0.0 – 192.168.255.255
4. Check Your IP

Run:

ip addr show

Example:

inet 192.168.1.10/24

My private IP: PASTE YOUR ACTUAL PRIVATE IP HERE

How I identified it:
The address falls within one of the private ranges above.

Task 3: CIDR & Subnetting
1. What does /24 mean?

In:

192.168.1.0/24

/24 means that the first 24 bits are the network portion of the IPv4 address.

The corresponding subnet mask is:

255.255.255.0

This leaves 8 bits for hosts.

2. Usable Hosts

The general calculation for a traditional IPv4 subnet is:

Usable hosts = 2^(host bits) - 2
CIDR	Subnet Mask	Total IPs	Usable Hosts
/24	255.255.255.0	256	254
/16	255.255.0.0	65,536	65,534
/28	255.255.255.240	16	14

The two unavailable addresses in the traditional subnet model are the network address and broadcast address.

3. Why do we subnet?

We subnet a network to divide a large network into smaller logical networks. This helps with IP address management, reducing broadcast traffic, improving organization, and controlling network access.

4. CIDR Exercise
CIDR	Subnet Mask	Total IPs	Usable Hosts
/24	255.255.255.0	256	254
/16	255.255.0.0	65,536	65,534
/28	255.255.255.240	16	14
Task 4: Ports – The Doors to Services
1. What is a port?

A port is a logical number used to identify a specific service or application on a device.

An IP address identifies the host, while the port helps identify the service running on that host.

For example:

10.0.1.50:3306

Here:

10.0.1.50 → IP address
3306 → port
MySQL → service
2. Common Ports
Port	Service
22	SSH
80	HTTP
443	HTTPS
53	DNS
3306	MySQL
6379	Redis
27017	MongoDB
3. Check Listening Ports

Run:

ss -tulpn

Example:

LISTEN ... 0.0.0.0:22 ... sshd
LISTEN ... 0.0.0.0:80 ... nginx

Record your actual output:

Port 1: _____ → Service: _____

Port 2: _____ → Service: _____

Task 5: Putting It Together
1. curl http://myapp.com:8080

Several networking concepts are involved:

DNS resolves myapp.com to an IP address.
Port 8080 identifies the application service.
curl uses HTTP to communicate with the application.
The connection uses TCP/IP underneath HTTP.
2. App can't reach 10.0.1.50:3306

I would first check whether the database host is reachable and whether port 3306 is accessible:

ping 10.0.1.50

Then:

nc -zv 10.0.1.50 3306

If the port is unreachable, I would check the MySQL service, firewall/security rules, routing, and whether MySQL is listening on the correct interface and port.

Quick Interview Revision

Remember this troubleshooting flow:

Domain
  ↓
DNS
  ↓
IP
  ↓
Route
  ↓
Port
  ↓
Service
  ↓
Application

For example:

myapp.com:8080
    ↓
DNS → IP address
    ↓
Network connectivity
    ↓
TCP port 8080
    ↓
Application listening on 8080
    ↓
HTTP response

This is a very useful flow to remember for DevOps and production troubleshooting.
