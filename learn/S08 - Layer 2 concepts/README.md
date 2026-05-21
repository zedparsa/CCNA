## 📖 Part 1 — Campus LAN Architecture

### 📝 Summary:
Campus LAN architecture is a hierarchical design model used in enterprise networks to improve scalability, performance, and manageability.  
Instead of connecting all devices randomly, the network is divided into logical layers, each responsible for specific tasks.

This model simplifies troubleshooting, improves fault isolation, and allows the network to scale as the organization grows.

### 🎯 Objectives:
- Understand the hierarchical campus LAN design model.
- Identify the responsibilities of each layer.
- Learn how traffic flows inside enterprise networks.
- Understand the concept of collapsed core architecture.

### 🧩 Topology:
Typical enterprise campus network containing access switches connected to distribution switches, which then connect to core switches that form the backbone of the network.


### 🛠️ Step-by-Step:

Campus LAN hierarchical design usually contains four layers:

**1️⃣ Access Layer**
This is the layer where end devices connect to the network.

Examples:
- PCs
- Printers
- IP Phones
- Wireless Access Points

Responsibilities:
- Device connectivity
- VLAN assignment
- Port security
- Basic switching operations


**2️⃣ Distribution Layer (Aggregation Layer)**

This layer aggregates multiple access switches and performs policy-based operations.

Responsibilities:
- Inter-VLAN routing
- Access control policies
- Filtering and QoS policies
- Route summarization


**3️⃣ Core Layer**

The core layer acts as the high-speed backbone of the network.

Responsibilities:
- Very fast packet forwarding
- High availability
- Minimal latency
- No heavy filtering or complex policies


**4️⃣ Edge Layer**

The edge layer represents the boundary between the internal network and external networks.

Examples:
- Internet connections
- WAN links
- Firewalls
- VPN gateways


### Collapsed Core Design

In small or medium networks, the **core layer and distribution layer can be merged** into a single layer.

This design is called:

Collapsed Core

Advantages:
- Lower cost
- Simpler topology
- Easier management

### ⚠️ Note:
The hierarchical design greatly improves scalability.  
Large enterprise networks almost always follow this model because it separates responsibilities between layers and prevents network complexity.

---
---
---

## 📖 Part 2 — VLAN (Virtual Local Area Network)

### 📝 Summary:
A VLAN (Virtual Local Area Network) is a logical segmentation of a Layer 2 network.  
It allows administrators to divide a physical switch into multiple broadcast domains.

Instead of every device belonging to one large network, VLANs allow devices to be grouped logically regardless of their physical location.

### 🎯 Objectives:
- Understand the concept of VLANs and broadcast domains.
- Learn why VLAN segmentation improves network performance and security.
- Create and manage VLANs on switches.
- Assign switch ports to specific VLANs.
- Verify VLAN configuration using IOS commands.

### 🧩 Topology:
One or more switches with multiple end devices connected to different VLANs (for example VLAN 10 for HR and VLAN 20 for IT).


### 🛠️ Step-by-Step:

### Why VLANs are Important

In a normal switch network without VLANs:

- All devices belong to the same broadcast domain.
- Broadcast traffic is sent to every device.

This can cause:
- Network congestion
- Security issues
- Difficult network management

By creating VLANs, we can:

- Reduce broadcast domains
- Improve security
- Organize the network logically

Example:
- VLAN 10 → HR department
- VLAN 20 → IT department
- VLAN 30 → Finance department

Devices in different VLANs **cannot communicate directly** without Layer 3 routing.


### Checking MAC Address Table

```cisco
Switch# show mac address-table
```

This command displays learned MAC addresses and the VLAN in which they were learned.

### Viewing VLAN Information

```cisco
Switch# show vlan brief
```

This command shows:
- VLAN ID
- VLAN name
- Status
- Assigned ports

By default, all switch ports belong to **VLAN 1**, which is the default VLAN.


### Creating a VLAN

Main method:

```cisco
Switch(config)# vlan 10
```

The switch creates VLAN 10 and assigns a default name.

### Changing VLAN Name

```cisco
Switch(config)# vlan 10
Switch(config-vlan)# name HR
```

This assigns a descriptive name to the VLAN.

### Assigning a VLAN to a Single Port

```cisco
Switch(config)# interface fa0/1
Switch(config-if)# switchport access vlan 10
```

This assigns interface Fa0/1 to VLAN 10.

### Assigning VLAN to Multiple Ports

```cisco
Switch(config)# interface range fa0/1 - 5
Switch(config-if-range)# switchport access vlan 10
```

This assigns interfaces Fa0/1 through Fa0/5 to VLAN 10.

### Automatic VLAN Creation

If you assign a port to a VLAN that does not yet exist:

```cisco
Switch(config)# interface fa0/10
Switch(config-if)# switchport access vlan 20
```

The switch will **automatically create VLAN 20**.

### VLAN Database File

If you check the flash storage:

```cisco
Switch# show flash:
```

You will see a file named: `vlan.dat`  

This file stores the VLAN database of the switch.

### ✅ Verification:

```cisco
Switch# show vlan brief
Switch# show mac address-table
Switch# show flash:
```

These commands allow you to verify:
- Existing VLANs
- MAC address learning
- VLAN database storage

### ⚠️ Note:

Devices inside the same VLAN can communicate directly at Layer 2.  
However, communication between different VLANs requires **Layer 3 routing**, usually implemented using:

- Router-on-a-Stick
- Layer 3 Switch (SVI)
