# Packet Tracer Lab: OSPF Configuration and Default Route Advertisement

## Lab Objectives
1. **Configure hostnames and IP addresses** on each router and enable the interfaces.
2. **Create loopback interfaces** on each router to simulate host addresses.
3. **Configure OSPF** on all routers, enabling it on all interfaces (except the ISP link) and marking loopback interfaces as passive.
4. **Configure R1** as an ASBR (Autonomous System Boundary Router) to advertise a default route into the OSPF domain.
5. **Verify the default routes** in the routing tables of R2, R3, and R4.

---

## Lab Topology Overview
- **R1**, **R2**, **R3**, and **R4** are the core routers.
- **SW1** is connected to **R4** and provides connectivity to **PC1**.
- **R1** connects to the ISP router (**ISPR1**), which is **outside the OSPF domain**.
- All routers are connected via point-to-point serial or Ethernet links with OSPF running in **Area 0**.

---

## Task 1: Configure Hostnames and IP Addresses

1. Assign the following hostnames to your routers:
   - **R1**, **R2**, **R3**, **R4**

2. Configure IP addresses on the routers as shown in the topology:
   - **R1**:
     - `G0/0` -> 10.0.12.1/30
     - `F1/0` -> 10.0.13.1/30
   - **R2**:
     - `G0/0` -> 10.0.12.2/30
     - `F1/0` -> 10.0.24.1/30
   - **R3**:
     - `F1/0` -> 10.0.13.2/30
     - `F2/0` -> 10.0.34.1/30
   - **R4**:
     - `F1/0` -> 10.0.24.2/30
     - `F2/0` -> 10.0.34.2/30
     - `G0/0` -> 192.168.4.254/24
   - **PC1**:
     - IP -> 192.168.4.1/24

3. **Enable router interfaces** by using the `no shutdown` command on each.

---

## Task 2: Configure Loopback Interfaces

For each router, configure a loopback interface to simulate a host address as follows:
- **R1**: `1.1.1.1/32`
- **R2**: `2.2.2.2/32`
- **R3**: `3.3.3.3/32`
- **R4**: `4.4.4.4/32`

### Commands Example for R1:
```bash
R1(config)# interface loopback 0
R1(config-if)# ip address 1.1.1.1 255.255.255.255
```

Repeat this for the other routers with their respective IP addresses.

---

## Task 3: OSPF Configuration

### Step 1: Configure OSPF on Each Router
- Use **OSPF process ID 1** on all routers.
- Enable OSPF on all **router interfaces** and ensure that the loopback interfaces are also included in the OSPF domain.

Example OSPF configuration for **R1**:
```bash
R1(config)# router ospf 1
R1(config-router)# network 10.0.12.0 0.0.0.3 area 0
R1(config-router)# network 10.0.13.0 0.0.0.3 area 0
R1(config-router)# network 1.1.1.1 0.0.0.0 area 0
```

### Step 2: Set Passive Interfaces
- Set the **loopback interfaces** and any interfaces not connected to OSPF routers as **passive**.

Example for R1:
```bash
R1(config-router)# passive-interface loopback 0
```

- Do not enable OSPF on the link between **R1** and **ISPR1**.

---

## Task 4: Configure R1 as an ASBR

On **R1**, configure the advertisement of a **default route** into the OSPF domain. This will allow the other routers (R2, R3, R4) to use R1 as a gateway to reach the internet.

1. First, configure a **static default route** pointing towards the ISP router (**ISPR1**):
```bash
R1(config)# ip route 0.0.0.0 0.0.0.0 203.0.113.2
```

2. Next, advertise the default route into the OSPF domain:
```bash
R1(config-router)# default-information originate
```

---

## Task 5: Verify the Routing Tables

On **R2**, **R3**, and **R4**, verify the presence of the default route learned via OSPF.

### Command:
```bash
R2# show ip route
```

Check that a **default route (0.0.0.0/0)** has been added via **R1**.

- The routing tables should show the default route pointing to R1's internal address (e.g., 10.0.12.1 for R2).

### Expected Output:
You should see the default route propagated to each router's routing table, allowing them to forward packets to **R1** for destinations outside the OSPF domain.

---

## Conclusion
This lab demonstrates how to configure OSPF across multiple routers, set up loopback interfaces, and advertise a default route using an ASBR. By the end of this lab, all internal routers (R2, R3, and R4) should have a default route pointing to R1, which provides a path to the internet.


## Acknowledgements


Special thanks to **Jeremy's IT Lab** for providing valuable resources and tutorials that greatly contributed to the completion of this exercise. His in-depth explanations and practical demonstrations have been instrumental in enhancing my understanding of Cisco networking concepts and the effective use of Packet Tracer.

For more information and additional resources, visit [Jeremy's IT Lab](https://jeremysitlab.com/) and check out his YouTube for the full course, [Jeremy's IT Lab Free CCNA 200-301 | Complete Course](https://www.youtube.com/playlist?list=PLxbwE86jKRgMpuZuLBivzlM8s2Dk5lXBQ)
