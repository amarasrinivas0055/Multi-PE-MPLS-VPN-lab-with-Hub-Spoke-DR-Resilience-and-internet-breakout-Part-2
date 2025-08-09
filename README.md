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

![Topology](

<img width="1759" height="909" alt="Image" src="https://github.com/user-attachments/assets/95d72a1c-5baa-423b-81eb-ee15ba37d916" />

)

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

Instance-1 : HUB to DR without backdoor link behavior :-
======================================

In this instance when link between HUB and PE1 goes down then my HUB becomes isolated and Hub to spoke communication goes down causing huge issue and communication blockage and business operations will get affected 

Topology :
------------
<img width="1365" height="864" alt="Image" src="https://github.com/user-attachments/assets/920cb5b7-4ea8-4800-a5bf-9fe79ef5ed0a" />

HUB and DR output without back door link :
-------------------------------------------------

Without backdoor link between HUB to DR the Hub routes to DR and DR routs to hub propogatins via PE1 Mpls path and DR roues in hub will be read as inter routes OIA and same in hub the DR routes are learned as OIA

Note : When PE1 to HUB link goes down entire organization  communication will go down 

<img width="1824" height="607" alt="Image" src="https://github.com/user-attachments/assets/edfce21a-5fb6-4645-ad72-0c2d538a6127" />


Instance-2 : HUB to DR with backdoor link behavior :-
======================================

The need for introducing backdoor link is when hub to PE1 fails i want redundancy and i want my spoke to communicate with my hub even when my hub to PE1 link goes down 

So i implemented to backdoor link and connected my Hub and DR using backdoor link in real time like leased link or point to point link 

Topology :
------------

<img width="1512" height="889" alt="Image" src="https://github.com/user-attachments/assets/3f2b9e37-45a3-4ca4-90b2-432deaff83d3" />

HUB and DR output without back door link :
-------------------------------------------------

When backdoor link become live my hub routes in DR and DR routs in hub are learned like O which LSA 1 and traffic between HUB and DR started flowing via backdoor link instead via MPLs path 

<img width="1872" height="656" alt="Image" src="https://github.com/user-attachments/assets/83701d2b-34a7-405b-947b-fd4441e3198b" />

As leased line are costly my bandwidth on leased line is less and this leased line need to come into action only when Hub to PE1 goes down, so to over come this issue 

I increased the OSPF cost on leased link but yet still traffic between HUB and DR is via Backdoor because LSA 1 is given first priority rather then cost 

<img width="1847" height="730" alt="Image" src="https://github.com/user-attachments/assets/866faf6a-d2db-4e14-85b8-5f3d29e98012" />

Hence to over come this issue and to make sure my traffic between hub and DR flow through MPLS infra i created SHAM LINK between PE1 to PE2 


Instance-3 : HUB and DR behavior post creating sham link between PE1 To PE5 :-
======================================================

Post creating sham link between PE1 and PE5 the traffic between HUB and DR started to flow via MPLSs infra because sham link is like extension of area 1 from PE1 to PE5 it manipulates the control plane and Hub and DR thinks this there is direct area 1 connection and as cost on Back door link is set to maximum 65535 traffic flow from MPLS infra 

Below are the images of hub to DE reachability routes and trace route supporting traffic is via MPLS infra 

<img width="1873" height="818" alt="Image" src="https://github.com/user-attachments/assets/7e4619e5-6a6d-45b5-a354-726432426ad9" />

<img width="1849" height="873" alt="Image" src="https://github.com/user-attachments/assets/2c93986f-f762-4095-a03d-6cc927b49cc3" />

Instance-4 : Spoke to HUB reachability via DR when Hub to PE1 link down 
=============================================

In this instance the link between hub and PE1 goes down and traffic between hub to spoke will propagate via DR and reach's hub with the help of Backdoor link, below are the output of all spokes and hub  when hub to PE1link goes down

Topology :
------------

<img width="1688" height="881" alt="Image" src="https://github.com/user-attachments/assets/5541efb7-080e-4909-91a0-1a2c45096ad9" />

Hub to all spoke reachability via DR and backdoor link when Hub to PE1 link down :
---------------------------------------------------------------------------------------------

<img width="919" height="493" alt="Image" src="https://github.com/user-attachments/assets/740d9298-0d54-44f8-8eb2-46b74ede0381" />

Spk1 to Hub reachability ping, Trace route output via DR when Hub to PE1 link is down :
---------------------------------------------------------------------------------------------

<img width="794" height="575" alt="Image" src="https://github.com/user-attachments/assets/b9236e36-1e12-4a8f-9091-991f3f65ee57" />

Spk2 to Hub reachability ping, Trace route output via DR when Hub to PE1 link is down :
---------------------------------------------------------------------------------------------

<img width="758" height="452" alt="Image" src="https://github.com/user-attachments/assets/40366d18-0ff5-4d3c-b36d-ce7dbbf5064e" />

Spk3 to Hub reachability ping, Trace route output via DR when Hub to PE1 link is down :
---------------------------------------------------------------------------------------------

<img width="811" height="374" alt="Image" src="https://github.com/user-attachments/assets/4b815983-f8fa-4c27-b465-b69a1031272a" />

HUB to all spoke trace route when Hub to PE1 link down :
---------------------------------------------------------------------------------------------

<img width="852" height="555" alt="Image" src="https://github.com/user-attachments/assets/96f03f65-8d41-4b9e-9526-fad938b3c8a1" />

all spoke routes reached hub via DR due to HUB to P1 link down :
---------------------------------------------------------------------------------------------

<img width="1121" height="993" alt="Image" src="https://github.com/user-attachments/assets/dff319c7-3915-437d-9a90-9086143828e3" />

Hub and DR learning routes via backdoor due to HUB to PE1 link down:
---------------------------------------------------------------------------------------------

<img width="1860" height="802" alt="Image" src="https://github.com/user-attachments/assets/f279db5b-daba-423e-ab24-c715a41f4205" />

Instance-5 :traffic fall back from spoke to Hub reachability via DR when Hub to PE down  to Traffic from hub to spoke via PE1 post Hub to PE1 link 
=============================================================================================
restoration 
=======

In this instance the link between hub and PE1 restore and traffic between hub to spoke will fallback via PE1, below is the topology and output supporting snaps 

Topology :
------------

<img width="1684" height="892" alt="Image" src="https://github.com/user-attachments/assets/003973b2-3ca3-4750-85dd-6e1eedee41fe" />

HUB To All Spoke Reachability via P1 post Hub to PE1 restored :
-----------------------------------------------------------------------

<img width="797" height="506" alt="Image" src="https://github.com/user-attachments/assets/e539e78b-5b24-434e-a306-2270d91b18d0" />

HUB To All Spoke Traceroute via P1 post Hub to PE1 restored :
-----------------------------------------------------------------------

<img width="800" height="538" alt="Image" src="https://github.com/user-attachments/assets/60185258-743b-4691-b5b9-9350ea8795b0" />

Hub Routes post Hub to PE1 link restored :
-----------------------------------------------------------------------

<img width="774" height="814" alt="Image" src="https://github.com/user-attachments/assets/c398245f-ff08-4c63-b2f9-93950860c9f6" /

Ping and trace route logs to hub from PE1 post Hub to PE1 link got restored :
-----------------------------------------------------------------------

<img width="1845" height="1031" alt="Image" src="https://github.com/user-attachments/assets/d2330847-6705-492b-b790-833d88a1abcb" />

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

