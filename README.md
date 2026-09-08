# 🌐 Cisco Network Engineering Portfolio

Kumpulan proyek simulasi dan implementasi arsitektur jaringan skala *Small-Medium Enterprise* (SME) serta *Enterprise Infrastructure* menggunakan Cisco Packet Tracer.

---

## 🏢 Project 1: Enterprise Network Gateway Redundancy with HSRP & Auto-DHCP

Mengimplementasikan infrastruktur jaringan tingkat enterprise yang memiliki ketahanan tinggi terhadap kegagalan perangkat (*High Availability / Zero Single Point of Failure*) menggunakan Hot Standby Router Protocol (HSRP). Sistem mampu melakukan *failover* otomatis tanpa mengganggu konektivitas pengguna akhir (*end-user*).

### 📐 Network Topology
<img width="701" height="376" alt="Screenshot 2026-09-08 182124" src="https://github.com/user-attachments/assets/d5fb199d-533a-44c5-8b17-32041351bd5e" />


### 🔑 Key Implementations
* **Hot Standby Router Protocol (HSRP):** Konfigurasi Group 1 dengan Priority 110 & Preempt pada R1-Master, serta Priority 100 pada R2-Backup.
* **Virtual Gateway IPv4:** Menggunakan Virtual IP `192.168.10.254/24` sebagai Default Gateway seluruh klien.
* **Failover Efficiency:** Transisi otomatis dari *active-to-standby* saat *disaster/link failure* dengan *downtime* sangat minim (hanya 1x RTO / < 2 detik).

### 📊 Addressing Table
| Device | Interface | Physical IP | Virtual IP (HSRP) | HSRP Priority | Role |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **R1-Master** | `Gi0/0` | `192.168.10.1/24` | `192.168.10.254` | 110 (Preempt) | Active Gateway (Primary) |
| **R2-Backup** | `Gi0/0` | `192.168.10.2/24` | `192.168.10.254` | 100 (Preempt) | Standby Gateway (Backup) |
| **PC-Client** | `Fa0` | DHCP (`192.168.10.3`) | `192.168.10.254` | - | End Host Client |

### 🧪 Failover Verification Test
<img width="400" height="300" alt="Screenshot 2026-09-08 182339" src="https://github.com/user-attachments/assets/34cb7f8b-3716-453e-93d7-080ffe7d2b04" />
<img width="400" height="300" alt="image" src="https://github.com/user-attachments/assets/143297c8-d8e6-429a-bac7-42ef4ba95a2b" />


---

## 🏢 Project 2: Secure SME Network Architecture – Inter-VLAN Routing & ACL Filtering

Mengimplementasikan arsitektur jaringan skala *Small-Medium Enterprise* (SME) dengan segmentasi trafik tingkat lanjut menggunakan Cisco 2911 Router dan Cisco 2960 Switch. Proyek ini memisahkan departemen Finance (VLAN 10) dan IT (VLAN 20) serta mengontrol akses antar-VLAN menggunakan Standard Access Control List (ACL).

### 📐 Network Topology
<img width="567" height="297" alt="Screenshot 2026-09-08 182915" src="https://github.com/user-attachments/assets/c185ed47-2c2a-4f42-9094-449f972e63ff" />


### 🛠️ Key Highlights & Features
* **Segmentasi VLAN & IEEE 802.1Q:** Mengisolasi *broadcast domain* antara Finance (VLAN 10) dan IT (VLAN 20) via Trunking Link `Fa0/24`.
* **Router-on-a-Stick (RoaS):** Sub-interface gateway `Gi0/0.10` dan `Gi0/0.20` pada R1-Gateway[cite: 4].
* **Automated DHCP Server:** Alokasi IP otomatis terpisah untuk segmen Finance (`192.168.10.0/24`) dan IT (`192.168.20.0/24`)[cite: 4].
* **Security ACL Filtering:** Menerapkan Standard ACL 1 pada Router Gateway untuk memblokir akses dari VLAN Finance ke VLAN IT[cite: 4].

### 📊 Network Addressing Plan
| Device | Interface | VLAN / Segment | IP Address / Subnet | Role / Function |
| :--- | :--- | :--- | :--- | :--- |
| **SW-Core** | `Fa0/1` | VLAN 10 (Finance) | Access Port | Connect to PC0 (Finance Client)[cite: 4] |
| **SW-Core** | `Fa0/2` | VLAN 20 (IT) | Access Port | Connect to PC1 (IT Client)[cite: 4] |
| **SW-Core** | `Fa0/24` | Trunk (802.1Q) | Native VLAN 1 | Uplink to R1-Gateway[cite: 4] |
| **R1-Gateway** | `Gi0/0.10` | VLAN 10 | `192.168.10.1/24` | Sub-Interface GW & DHCP Pool[cite: 4] |
| **R1-Gateway** | `Gi0/0.20` | VLAN 20 | `192.168.20.1/24` | Sub-Interface GW & DHCP Pool[cite: 4] |

### 💻 Core CLI Configurations

```cisco
# ==========================================
# SW-Core Configuration (VLAN & Trunking)
# ==========================================
vlan 10
 name Finance
vlan 20
 name IT
exit

interface FastEthernet0/1
 switchport access vlan 10
 switchport mode access

interface FastEthernet0/2
 switchport access vlan 20
 switchport mode access

interface FastEthernet0/24
 switchport mode trunk

# ==========================================
# R1-Gateway Configuration (RoaS & ACL)
# ==========================================
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
 ip access-group 1 out

# Security Filter: Deny Finance (VLAN 10) to reach IT (VLAN 20)
access-list 1 deny 192.168.10.0 0.0.0.255
access-list 1 permit any
