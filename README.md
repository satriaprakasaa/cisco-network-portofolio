# Cisco Network Engineering Portfolio

Kumpulan proyek simulasi dan implementasi arsitektur jaringan skala Small-Medium Enterprise (SME) serta Enterprise Infrastructure menggunakan Cisco Packet Tracer.

---

## 📌 Proyek 1: Enterprise Network Gateway Redundancy with HSRP & Auto-DHCP

### Overview
Mengimplementasikan infrastruktur jaringan enterprise dengan tingkat ketersediaan tinggi (*High Availability / Zero Single Point of Failure*) menggunakan Hot Standby Router Protocol (HSRP). Sistem melakukan failover otomatis saat terjadi gangguan pada router utama tanpa mengganggu konektivitas *end-user*.

### Topology & Addressing Table
* **Active Router (R1-Master):** 192.168.10.1/24 (Priority 110, Preempt)
* **Standby Router (R2-Backup):** 192.168.10.2/24 (Priority 100, Preempt)
* **Virtual Gateway IP:** 192.168.10.254/24

### Key Configurations
```cisco
! R1-Master Configuration
interface GigabitEthernet0/0
 ip address 192.168.10.1 255.255.255.0
 standby 1 ip 192.168.10.254
 standby 1 priority 110
 standby 1 preempt
!
ip dhcp pool LAN-HSRP
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.254
