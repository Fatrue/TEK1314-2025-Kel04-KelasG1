## Network Configuration Overview

**Subnet Network:** 192.168.4.0/24  
**Subnet Mask:** 255.255.255.0  
**Network Type:** Host-Only (Virtual Lab Environment)  

---

## Asset & IP Assignment

| Hostname        | Role                | IP Address      | OS                    | Main Service | Open Ports |
|-----------------|--------------------|-----------------|-----------------------|--------------|------------|
| attacker-04     | Attacker Node       | 192.168.4.100   | Kali Linux            | Attack Tools | - |
| fileserver-04   | Target SMB Server   | 192.168.4.5     | Ubuntu Server 22.04   | Samba (SMB)  | 445, 139, 22 |
| monitor-04      | Monitoring (SIEM)   | 192.168.4.10    | Security Onion        | IDS/SIEM     | 22 |

---

## Service Exposure Plan

- **SMB (TCP 445)** → File sharing service (Target Server)
- **NetBIOS (TCP 139)** → SMB legacy support
- **SSH (TCP 22)** → Remote management (limited access)
- **ICMP** → Connectivity testing only

---

## Security Notes

- Seluruh node berada dalam isolated virtual network (Host-Only).
- Tidak ada koneksi ke jaringan kampus/internet saat fase attack.
- Simulasi ransomware hanya dilakukan pada direktori `/shared/dummy_files`.
