# Day-06-STP
# CCNA Lab Day 6 – Spanning Tree Protocol (STP Fundamentals)

## Overview

This lab introduces Spanning Tree Protocol (STP), a Layer 2 loop prevention mechanism used in switched networks. STP prevents broadcast storms, MAC address table instability, and duplicate frame issues by blocking redundant paths while maintaining network redundancy.

This is one of the most important switching technologies in enterprise networks.

---

## Objectives

After completing this lab, you will be able to:

- Understand Layer 2 loops
- Understand Broadcast Storms
- Understand STP operation
- Identify the Root Bridge
- Understand Bridge ID and Root Bridge Election
- Identify Root Ports and Designated Ports
- Understand Blocking Ports
- Verify STP operation using Cisco commands
- Perform basic STP troubleshooting

---

## Network Topology

```text
                SW1
               /   \
              /     \
             /       \
         SW2---------SW3


PC1                   PC2
 |                     |
SW2                   SW3
```

---

## Devices Used

- 3 Cisco Switches
- 2 PCs
- Copper Straight-Through Cables

---

## Physical Connections

| Device | Interface | Connected To |
|----------|----------|----------|
| SW1 Fa0/1 | SW2 Fa0/1 |
| SW1 Fa0/2 | SW3 Fa0/1 |
| SW2 Fa0/2 | SW3 Fa0/2 |
| PC1 Fa0 | SW2 Fa0/10 |
| PC2 Fa0 | SW3 Fa0/10 |

---

## IP Addressing

### VLAN 10

| Device | IP Address |
|----------|----------|
| PC1 | 192.168.10.10 |
| PC2 | 192.168.10.20 |

Subnet Mask:

```text
255.255.255.0
```

---

## Step 1: Create VLAN 10

Configure on all switches:

```bash
enable
configure terminal

vlan 10
name USERS
```

---

## Step 2: Configure Access Ports

### SW2

```bash
interface fa0/10
switchport mode access
switchport access vlan 10
```

### SW3

```bash
interface fa0/10
switchport mode access
switchport access vlan 10
```

---

## Step 3: Configure Trunk Links

### SW1

```bash
interface range fa0/1 - 2
switchport mode trunk
```

### SW2

```bash
interface range fa0/1 - 2
switchport mode trunk
```

### SW3

```bash
interface range fa0/1 - 2
switchport mode trunk
```

---

## Why STP Is Needed

The topology contains multiple paths between switches.

Without STP:

```text
Broadcast Frame
      ↓
     SW1
    ↙   ↘
  SW2   SW3
    ↖   ↙
      Loop
```

This creates:

- Broadcast Storms
- Duplicate Frames
- MAC Address Table Instability
- Network Outages

STP prevents these issues by blocking one redundant path.

---

## Understanding Root Bridge

Every STP topology has one Root Bridge.

The Root Bridge acts as the central reference point for all path calculations.

STP elects the Root Bridge based on:

1. Lowest Bridge Priority
2. Lowest MAC Address

Default Priority:

```text
32768
```

If all switches use the default priority, the switch with the lowest MAC address becomes the Root Bridge.

---

## Verify Root Bridge

Run on all switches:

```bash
show spanning-tree
```

Look for:

```text
This bridge is the root
```

The switch displaying this message is the Root Bridge.

---

## STP Port Roles

### Root Port (RP)

The Root Port is the best path toward the Root Bridge.

Characteristics:

- One Root Port per non-root switch
- Always forwards traffic

Example:

```text
SW2 ---- Root Port ---- SW1
```

---

### Designated Port (DP)

The Designated Port forwards traffic for a network segment.

Characteristics:

- One Designated Port per segment
- Forwards traffic

---

### Blocking Port

The Blocking Port prevents loops.

Characteristics:

- Receives BPDUs
- Does not forward user traffic

Example:

```text
SW2 --------X-------- SW3
```

The blocked port prevents a Layer 2 loop.

---

## Expected STP Topology

After STP converges:

```text
              SW1
             /   \
            /     \
           /       \
        SW2   X   SW3
```

One redundant path is blocked.

The network remains operational while avoiding loops.

---

## Verification Commands

### View STP Information

```bash
show spanning-tree
```

### View Root Bridge Information

```bash
show spanning-tree root
```

### View STP Summary

```bash
show spanning-tree summary
```

---

## Understanding STP Port States

During convergence, ports move through several states.

```text
Blocking
   ↓
Listening
   ↓
Learning
   ↓
Forwarding
```

### Blocking

- Receives BPDUs
- Does not forward traffic

### Listening

- Processes STP information
- No MAC address learning

### Learning

- Learns MAC addresses
- Does not forward traffic

### Forwarding

- Normal operational state
- Forwards traffic
- Learns MAC addresses

---

## Configure SW1 as Root Bridge

On SW1:

```bash
configure terminal

spanning-tree vlan 10 root primary
```

Verify:

```bash
show spanning-tree
```

Expected Output:

```text
This bridge is the root
```

---

## Connectivity Testing

### Test PC-to-PC Communication

From PC1:

```bash
ping 192.168.10.20
```

Expected Result:

Success

---

## Traffic Flow Example

Traffic from PC1 to PC2:

```text
PC1
 |
SW2
 |
SW1
 |
SW3
 |
PC2
```

The blocked link is not used during normal operation.

---

## Troubleshooting Scenarios

### Scenario 1: Identify Root Bridge

Run:

```bash
show spanning-tree
```

Identify:

- Root Bridge
- Root Ports
- Designated Ports
- Blocking Ports

---

### Scenario 2: Force SW2 as Root Bridge

On SW2:

```bash
configure terminal

spanning-tree vlan 10 root primary
```

Verify:

```bash
show spanning-tree
```

Observe how STP recalculates the topology.

---

### Scenario 3: Link Failure Test

Disconnect:

```text
SW1 ↔ SW2
```

Observe:

- Previously blocked port becomes active
- Network connectivity remains operational

This demonstrates STP redundancy and failover.

---

## Mini Challenge

Build the following topology:

```text
           SW1
          / | \
         /  |  \
       SW2 SW3 SW4
```

Tasks:

- Create VLAN 10
- Configure access ports
- Configure trunk links
- Determine the Root Bridge
- Identify blocked ports
- Force SW1 to become Root Bridge
- Verify STP operation using show commands

---

## Skills Learned

- Layer 2 Loop Prevention
- Broadcast Storm Prevention
- STP Fundamentals
- Root Bridge Election
- Bridge Priority
- Root Ports
- Designated Ports
- Blocking Ports
- STP Verification
- STP Troubleshooting

---

## Conclusion

This lab introduced the fundamentals of Spanning Tree Protocol and demonstrated how enterprise switches prevent Layer 2 loops while maintaining redundant paths. Understanding STP is essential for designing reliable and scalable switched networks.

---

## Next Lab

### Day 7 – STP Root Bridge Control, PortFast, and BPDU Guard

Topics:

- Manual Root Bridge Selection
- PortFast Configuration
- BPDU Guard
- Edge Ports
- STP Security Best Practices
- Enterprise Switching Design

---

## Author

Muhammad Kausar

CCNA Enterprise Networking Lab Series
