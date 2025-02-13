# **[RDS - 2425 Task #1]: From Hub to L2 Switch**

This repository contains starter code for a simple network device written in P4. Currently, the device behaves as a **hub**, broadcasting all incoming packets to all connected ports. 

Your task is to transform this hub into an **L2 switch** that forwards packets based on a **static MAC address table**. The table will be populated by the control plane when the switch is initialized.

In this taks, the control plane is emulated by the **simple_switch_CLI** program.

---

## **Objectives**
- Understand the difference between **hub** and **L2 switch** behavior.
- Introduction to Mininet.
- Learn how to use **P4 tables** for exact-match lookups.
- Learn how to use **P4 actions**.

---
## **Topology**
     h1    h2
       \  /
        s1
       /  \
     h3    h4
## **Task Description**

### **Create the topology**:
- change the `mininet/task1-topo.py` to implement the above topology.

### **Hub Behavior (Current State)**:
The provided `p4/hub.p4` program:
- **Broadcasts** every packet it receives to all ports except the ingress port.
- Does not inspect or process any packet headers.
- Understand the flows defined in `flows/s1-flows.txt`

### **Test your Hub setup**
1. **Compile the P4 code**
```bash
p4c-bm2-ss --std p4-16  p4/hub.p4 -o json/hub.json
```
2. **Run Mininet script**
```bash
sudo python3 mininet/task1-topo.py --json json/hub.json
```
3. **Inject the flows rules (your controller for now)**
```bash
simple_switch_CLI --thrift-port 9090 < flows/s1-flows.txt
``` 
4. **Test**
```bash
mininet> h1 ping h3 -c 5
```

### **L2 Switch Behavior (Your Goal)**:
- Create a copy of hub.p4 and name it l2_switch.p4
- Modify l2_switch.p4 to implement an L2 switch, which involves:
1. **MAC Address Table**:
   - Create a table that maps destination MAC addresses to specific egress ports.
   - This table will be populated by the control plane.
2. **Forwarding Logic**:
   - For packets with a **known destination MAC**, forward them to the corresponding port.
   - For packets with an **unknown destination MAC**, broadcast them to all ports except the ingress port.

3. **Rules**
   - Extend `flows/s1-flows.txt` to define the necessary flow rules for populating  
the tables of your L2 switch.
   - Syntax: 
   - `table_add <table_name> <action_name> <key> => <arguments_for_the_action>`

### **Test your L2 switch setup**
   - Follow the same testing procedure used for the hub setup.
   - Ensure you can identify the behavioral differences between the hub and the L2 switch.
   - Refer to the `Debugging Tips` section for guidance on analyzing these differences.

## Debugging Tips

Here are some useful commands to help troubleshoot and verify your topology:

### 1. **Wireshark (Packet Capture)**

### 2. **ARP Table Inspection**
   - **Command:** `arp -n`
   - **Usage:** Check the ARP table on any Mininet host to ensure proper IP-to-MAC Default gateway resolution.
   - Example:
     ```bash
     mininet> h1 arp -n
     ```

### 3. **Interface Information**
   - **Command:** `ip link`
   - **Usage:** Display the state and configuration of network interfaces for each host or router.
   - Example:
     ```bash
     mininet> r1 ip link
     ```

### 4. **P4 Runtime Client for Monitoring**
   - **Tool:** `nanomsg_client.py`
   - **Usage:** Interact with the P4 devices at runtime to inspect flow tables and rules loaded in to the device.
   - Example:
     ```bash
     sudo ./tools/nanomsg_client.py --thrift-port 9090
     ```

These commands will help you inspect network traffic, verify ARP entries, check interface states, and interact with P4 devices.