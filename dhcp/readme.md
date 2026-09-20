## What is DHCP?

**DHCP (Dynamic Host Configuration Protocol)** is a network protocol that automatically provides devices with network configuration, such as an **IP address, subnet mask, default gateway, and DNS server**.

Instead of manually configuring the IP address on every computer, a DHCP server automatically assigns the required network configuration.

## Lab Topology

```text
                 ┌───────────────┐
                 │  DHCP Server  │
                 │ 192.168.1.2   │
                 └───────┬───────┘
                         │
                         │ Ethernet
                         │
                    ┌────▼────┐
                    │ Switch  │
                    └─┬──┬──┬─┘
                      │  │  │
                    ┌─▼┐┌▼─┐┌▼─┐
                    │PC1││PC2││PC3│
                    └──┘└──┘└──┘
```

## DHCP Configuration

On the **DHCP Server**:

1. Open the **Server**.
2. Go to **Services → DHCP**.
3. Turn **DHCP ON**.
4. Create a DHCP pool.
5. Configure the network and IP address range.

Example:

```text
Pool Name:          LAN
Start IP Address:   192.168.1.10
Subnet Mask:        255.255.255.0
Default Gateway:    192.168.1.1
DNS Server:         8.8.8.8
Maximum Users:      50
```

## Configure the PCs

On each PC:

```text
PC → Desktop → IP Configuration → DHCP
```

The PC sends a DHCP request to obtain its network configuration.

The DHCP server responds with an available IP address and the configured network parameters.

For example:

```text
PC1 → 192.168.1.10
PC2 → 192.168.1.11
PC3 → 192.168.1.12
```

## DHCP Workflow

```text
PC
 │
 │ DHCP Request
 ▼
Switch
 │
 ▼
DHCP Server
 │
 │ DHCP Offer / Configuration
 ▼
Switch
 │
 ▼
PC receives IP configuration
```

### Result

The PCs successfully obtain their IP addresses automatically from the DHCP server without manually configuring an IP address on each computer.

**Key Concept:**

> DHCP automatically assigns network configuration to devices, reducing the need for manual IP configuration.
