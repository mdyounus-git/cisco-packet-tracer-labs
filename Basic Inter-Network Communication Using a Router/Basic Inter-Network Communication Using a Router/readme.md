````markdown
# Basic Inter-Network Communication Using a Router

A Cisco Packet Tracer network demonstrating communication between two different IP networks using a router as the default gateway.

## Network Overview

This topology contains two separate networks connected through a router:

```text
                    ROUTER
             ┌──────────────────┐
             │ G0/0  10.1.1.9   │
             │ G0/1  192.168.1.3 │
             └────────┬─────────┘
                      │
          ┌───────────┴───────────┐
          │                       │
       Switch                 Switch0
          │                       │
      PCs/Clients              Servers
   10.1.1.0/24             192.168.1.0/24
````

### IP Addressing

| Device/Network | IP Address       |
| -------------- | ---------------- |
| PC Network     | `10.1.1.0/24`    |
| Router G0/0    | `10.1.1.9/24`    |
| Router G0/1    | `192.168.1.3/24` |
| Server Network | `192.168.1.0/24` |
| Server0        | `192.168.1.1`    |
| Server1        | `192.168.1.4`    |
| Server2        | `192.168.1.5`    |
| Server3        | `192.168.1.6`    |
| Server4        | `192.168.1.7`    |
| DNS            | `8.8.8.8`        |

---

# 1. Configuring IP Address on PCs

In Cisco Packet Tracer:

```text
PC → Desktop → IP Configuration
```

Configure the PC with an IP address belonging to the `10.1.1.0/24` network.

Example:

```text
IP Address:       10.1.1.5
Subnet Mask:      255.255.255.0
Default Gateway:  10.1.1.9
DNS Server:       8.8.8.8
```

Each PC should have a different IP address, but all PCs in this network use the same default gateway:

```text
10.1.1.9
```

The reason is that all these PCs belong to the same local network:

```text
10.1.1.0/24
```

and the router interface connected to this network is:

```text
10.1.1.9
```

Therefore, `10.1.1.9` is the gateway used by all PCs when they need to communicate outside their local network.

---

# 2. Why Is a Default Gateway Required?

A PC does not send every packet directly to the router.

First, it checks whether the destination belongs to its own network.

For example:

```text
PC3:
10.1.1.5

Destination:
192.168.1.7
```

PC3 determines that `192.168.1.7` is outside its local network.

It therefore needs a device that can forward the packet to another network.

That device is the router.

```text
PC3
10.1.1.5
   |
   | Local network
   ↓
Switch
   |
   ↓
Router
10.1.1.9
   |
   | Routes to another network
   ↓
192.168.1.0/24
```

The default gateway tells the PC:

> "When the destination is outside my local network, send the packet to this router."

For this network:

```text
PCs → Default Gateway: 10.1.1.9
```

The gateway is the same for all PCs because they are connected to the same network and use the same router interface to leave that network.

---

# 3. Configuring the Router

Open the router CLI.

First enter privileged mode:

```text
enable
```

Then enter global configuration mode:

```text
configure terminal
```

Select the interface connected to the PC network:

```text
interface gigabitEthernet 0/0
```

Assign the IP address and subnet mask:

```text
ip address 10.1.1.9 255.255.255.0
```

Enable the interface:

```text
no shutdown
```

Now configure the interface connected to the server network:

```text
interface gigabitEthernet 0/1
```

Assign its IP address:

```text
ip address 192.168.1.3 255.255.255.0
```

Enable it:

```text
no shutdown
```

The final router configuration is therefore:

```text
G0/0 → 10.1.1.9/24
G0/1 → 192.168.1.3/24
```

The router now has an interface in both networks and can route traffic between them.

---

# 4. Useful Router CLI Commands

### Check interface status and IP addresses

```text
show ip interface brief
```

This is useful for quickly checking whether the interfaces are:

```text
up/up
```

and whether the correct IP addresses have been assigned.

### Check detailed interface information

```text
show interfaces
```

This also displays the interface's hardware/MAC address.

To check a particular interface:

```text
show interfaces gigabitEthernet 0/0
```

### Check the router's ARP table

```text
show arp
```

This shows the relationship between IP addresses and MAC addresses that the router has learned.

### Check the routing table

```text
show ip route
```

For this topology, the router should have directly connected routes for:

```text
10.1.1.0/24
192.168.1.0/24
```

---

# 5. Configuring the Servers

For each server:

```text
Server → Desktop → IP Configuration
```

Example for Server4:

```text
IP Address:       192.168.1.7
Subnet Mask:      255.255.255.0
Default Gateway:  192.168.1.3
DNS Server:       8.8.8.8
```

Other servers use different IP addresses but the same gateway:

```text
Server0 → 192.168.1.1
Server1 → 192.168.1.4
Server2 → 192.168.1.5
Server3 → 192.168.1.6
Server4 → 192.168.1.7

Gateway → 192.168.1.3
DNS     → 8.8.8.8
```

The gateway is different from the PCs because the servers are on a different network.

```text
PC Network:
10.1.1.0/24
Gateway:
10.1.1.9

Server Network:
192.168.1.0/24
Gateway:
192.168.1.3
```

Each gateway is the router interface belonging to that particular network.

---

# 6. How a Packet Travels from PC3 to Server4

Consider:

```text
PC3:
IP = 10.1.1.5

Server4:
IP = 192.168.1.7
```

PC3 sends:

```text
ping 192.168.1.7
```

### Step 1 — PC3 checks the destination

PC3 compares its own network with the destination.

```text
PC3 network:
10.1.1.0/24

Destination:
192.168.1.7
```

The destination is outside the local network.

PC3 therefore decides to send the packet to its default gateway:

```text
10.1.1.9
```

### Step 2 — PC3 finds the gateway's MAC address

PC3 needs the router's MAC address to communicate with it over Ethernet.

If the MAC address is not already known, PC3 uses ARP:

```text
Who has 10.1.1.9?
```

The router responds with the MAC address of its G0/0 interface.

### Step 3 — PC3 creates the IP packet

The IP packet contains:

```text
Source IP:
10.1.1.5

Destination IP:
192.168.1.7
```

The destination IP **does not change to 10.1.1.9**.

`192.168.1.7` remains the final destination.

### Step 4 — PC3 creates the Ethernet frame

The Ethernet frame contains:

```text
Source MAC:
PC3's MAC

Destination MAC:
Router G0/0's MAC
```

So at this point:

```text
Ethernet Frame
┌─────────────────────────────────┐
│ Destination MAC → Router        │
│ Source MAC      → PC3           │
│                                 │
│ IP Packet                       │
│ Source IP      → 10.1.1.5       │
│ Destination IP → 192.168.1.7    │
└─────────────────────────────────┘
```

### Step 5 — The frame reaches the switch

```text
PC3
 ↓
Switch
 ↓
Router G0/0
```

The switch looks at the destination MAC address and forwards the frame toward the router.

The switch does not route the packet between IP networks.

Its job here is to forward the Ethernet frame to the correct port.

### Step 6 — Router receives the packet

The router receives the frame through:

```text
G0/0
10.1.1.9
```

The router examines the destination IP:

```text
192.168.1.7
```

Its routing table tells it that:

```text
192.168.1.0/24
```

is reachable through:

```text
G0/1
```

### Step 7 — Router forwards the packet

The router sends the packet through G0/1 toward the server network.

The IP destination remains:

```text
192.168.1.7
```

However, a new Ethernet frame is created for the server-side network.

The frame is then sent:

```text
Router
 ↓
Switch0
 ↓
Server4
```

### Complete flow

```text
PC3
10.1.1.5
   │
   │ Ethernet frame
   ▼
Switch
   │
   ▼
Router G0/0
10.1.1.9
   │
   │ Routing
   ▼
Router G0/1
192.168.1.3
   │
   ▼
Switch0
   │
   ▼
Server4
192.168.1.7
```

The important thing to remember is:

```text
Destination IP = Final destination
Destination MAC = Next device on the current network
```

So the destination IP remains `192.168.1.7` while the packet travels through the router, but the Ethernet/MAC information changes as the packet moves from one network to another.

---

# 7. Testing the Network

From a PC, test the local router interface:

```text
ping 10.1.1.9
```

Then test the router's server-side interface:

```text
ping 192.168.1.3
```

Finally, test a server:

```text
ping 192.168.1.7
```

If all three work, communication between the two networks is functioning correctly.

---

## Key Concept

The switch provides connectivity within the local network, while the router connects different IP networks.

The default gateway is necessary because the PC needs to know **which Layer-3 device to send traffic to when the destination is outside its own network**.

In this topology:

```text
10.1.1.0/24
      │
      │ Gateway: 10.1.1.9
      ▼
    Router
      ▲
      │ Gateway: 192.168.1.3
      │
192.168.1.0/24
```

This allows the PCs and servers on the two different networks to communicate with each other.


