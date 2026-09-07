# Cisco Network Engineering Portfolio

Kumpulan proyek simulasi dan implementasi arsitektur jaringan skala Small-Medium Enterprise (SME) serta Enterprise Infrastructure menggunakan Cisco Packet Tracer.

---

## 📌 Proyek 1: Enterprise Network Gateway Redundancy with HSRP & Auto-DHCP

### Overview
Mengimplementasikan infrastruktur jaringan enterprise dengan tingkat ketersediaan tinggi (*High Availability / Zero Single Point of Failure*) menggunakan Hot Standby Router Protocol (HSRP). Sistem melakukan failover otomatis saat terjadi gangguan pada router utama tanpa mengganggu konektivitas *end-user*.

### Network Topology
<!-- TARIK DAN LEPAS (DRAG & DROP) GAMBAR TOPOLOGI HSRP KAMU DI BAWAH INI -->
<img width="1360" height="690" alt="image" src="https://github.com/user-attachments/assets/dd05d1fd-e4b9-4cee-aa44-05a97843d799" />


### Addressing & Device Specifications
| Perangkat | Interface | IP Address Physical | Virtual IP (HSRP) | HSRP Priority | Peran System |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **R1-Master** | Gi0/0 | 192.168.10.1 /24 | 192.168.10.254 | 110 (Preempt) | Active Gateway (Primary) |
| **R2-Backup** | Gi0/0 | 192.168.10.2 /24 | 192.168.10.254 | 100 (Preempt) | Standby Gateway (Backup) |
| **PC-Client** | Fa0 | DHCP (192.168.10.3) | Default GW: .254 | - | End Host Client |

### Key CLI Configurations
```cisco
! R1-Master Configuration
interface GigabitEthernet0/0
 ip address 192.168.10.1 255.255.255.0
 no shutdown
 standby 1 ip 192.168.10.254
 standby 1 priority 110
 standby 1 preempt
!
ip dhcp excluded-address 192.168.10.1 192.168.10.2
ip dhcp excluded-address 192.168.10.254
ip dhcp pool LAN-HSRP
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.254
 dns-server 8.8.8.8
