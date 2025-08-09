# 🚀 Multi-PE MPLS VPN Lab – Part 2: DR Resilience with Sham-Link & Backdoor OSPF

## 📘 Overview

This is **Part 2** of my multi-stage **MPLS L3 VPN Lab** built in **EVE-NG**.  
Following **Part 1** (Hub-to-Spoke reachability with RT control and isolation), this stage focuses on **resiliency and redundancy**.

In this part, I add a **Disaster Recovery (DR)** site and ensure that if the **Hub-to-PE1 link fails**, spokes can still reach the Hub **via the DR site** using **OSPF Sham-Link** and a **backdoor path**.

> ⚙️ Technologies: MPLS, MP-BGP VPNv4, OSPF Sham-Link, Route Target control, IGP failover, MPLS VPN resiliency.

---

## 🎯 Objectives

- Maintain Hub-to-Spoke reachability **even during Hub-to-PE1 failure**.
- Implement **OSPF Sham-Link** to make MPLS path preferred over backdoor when primary is up.
- Use **backdoor link** between Hub CE and DR CE for failover.
- Keep **spoke-to-spoke isolation** intact.

---

## 🧱 Topology

- **1 Hub (CE)** – R6  
- **3 Spokes (CE)** – R7, R8, R9  
- **1 DR (CE)** – R10  
- **5 PEs** – R1–R5
- **MPLS Core** – Full LDP + IGP

![Topology](topology_part2.png)

---

## 🧠 Technologies Used

| Component         | Protocol / Feature  |
|-------------------|----------------------|
| Transport         | MPLS (LDP)           |
| PE-PE Routing     | MP-BGP (VPNv4)       |
| PE-CE Routing     | OSPF                 |
| Segmentation      | VRF with RD/RT       |
| Route Control     | RT Import/Export     |
| Resilience        | OSPF Sham-Link       |
| Failover Path     | Backdoor CE-to-CE    |
| Platform          | EVE-NG               |

---

## 📊 Test Scenarios & Results

### **Instance 1 – Normal Operation**
- Primary path: Hub ↔ PE1 ↔ MPLS ↔ Spokes
- DR path idle  
![Instance1](instance1.png)

---

### **Instance 2 – Hub-to-PE1 Failure**
- Hub loses PE1 connectivity  
- Spokes route Hub traffic via DR using backdoor OSPF  
![Instance2](instance2.png)

---

### **Instance 3 – Sham-Link Verification**
- Sham-Link ensures MPLS path preferred over backdoor when up  
![Instance3](instance3.png)

---

### **Instance 4 – Full Failover**
- Hub-to-PE1 down + MPLS failure detected
- DR provides Hub route to all spokes
![Instance4](instance4.png)

---

### **Instance 5 – Recovery & Fallback**
- Hub-to-PE1 restored
- Traffic shifts back to primary MPLS path automatically  
![Instance5](instance5.png)

---

## 🔍 Key Observations

- **Sham-Link** prevents OSPF from preferring the backdoor link when MPLS path is active.
- **Backdoor link** provides immediate failover during PE link loss.
- **RT control** still ensures Spokes are isolated from each other.
- **Convergence time** is minimal and routing tables update as expected.

---

## 📂 Repository Structure

```bash
mpls-l3vpn-part2-dr-resilience/
├── README.md
├── topology_part2.png
├── Multi-PE-MPLS-VPN-DR-Resilience.unl
├── configs/
│   ├── PE1.txt
│   ├── PE2.txt
│   ├── HUB.txt
│   ├── DR.txt
│   ├── SPK1.txt
│   └── ...
├── outputs/
│   ├── instance1.png
│   ├── instance2.png
│   ├── instance3.png
│   ├── instance4.png
│   ├── instance5.png
│   └── ...
```

---

## 🚧 Next Steps

### ✅ Part 3: Internet Breakout + Dual ISP Failover
- Provide Internet to all spokes via Hub
- Implement NAT at Hub and DR
- Failover between ISPs using IP SLA + Track

---

## 🙌 Credits

Created by **Amara Srinivas** – Network Engineer & Lab Enthusiast  
🔗 Connect with me on [LinkedIn](https://www.linkedin.com)

---

## 📎 License

This project is for **educational & demonstration purposes only**.
