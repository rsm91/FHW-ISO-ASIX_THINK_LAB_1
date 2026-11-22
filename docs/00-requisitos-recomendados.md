# Requisitos recomendados

Requisitos oficiales de los sistemas operativos usados en el entorno de pruebas individual creado en **VirtualBox**.

| Sistema | CPU | RAM mínima | RAM recomendada | Disco | Fuente oficial |
|---|---|---|---|---|---|
| **OPNsense 26.7** | 1 o más núcleos, 64 bits | 3 GB (en VM) | 4 GB | 8 GB | [Instalación virtual](https://docs.opnsense.org/manual/virtuals.html) · [Requisitos](https://opnsense.org/get-started/) |
| **MikroTik RouterOS 7 (CHR)** | 64 bits con virtualización | 512 + 8 × CPU × (interfaces − 1) MB | 1024 MB | 128 MB | [Instalación de CHR](https://help.mikrotik.com/docs/x/AYDtC) |
| **Debian 13 «Trixie»** (sin escritorio) | 64 bits | 512 MB | 1 GB | 4 GB | [Requisitos mínimos](https://www.debian.org/releases/trixie/amd64/ch03s04.en.html) |
| **TrueNAS 25.10** | 2 núcleos, 64 bits | 8 GB | 8 GB | 16 GB de arranque + 2 discos de datos iguales | [Guía de hardware](https://www.truenas.com/docs/scale/25.10/gettingstarted/scalehardwareguide/) |
| **Windows Server 2025** (con escritorio) | 1,4 GHz, 64 bits | 2 GB | 4 GB | 32 GB (64 GB recomendado) | [Requisitos de hardware](https://learn.microsoft.com/en-us/windows-server/get-started/hardware-requirements) |
| **Zabbix 8.0 LTS** | 2 núcleos (instalación pequeña) | 128 MB (punto de partida) | 8 GB para 1.000 métricas | 256 MB libres (punto de partida) | [Requisitos](https://www.zabbix.com/documentation/current/manual/installation/requirements) |
| **Proxmox VE 9.2** | 64 bits con Intel VT o AMD-V | 2 GB + RAM de las VM | Según las VM | 16 GB (sistema) | [Requisitos](https://www.proxmox.com/en/products/proxmox-virtual-environment/requirements) · [Alta disponibilidad](https://pve.proxmox.com/wiki/High_Availability) |
| **Windows 11** | 1 GHz, 2 núcleos, 64 bits | 4 GB | 4 GB | 64 GB · UEFI compatible con Secure Boot · TPM 2.0 | [Requisitos](https://www.microsoft.com/en-us/windows/windows-11-specifications) |
| **Debian 13 «Trixie»** (con escritorio) | 64 bits | 1 GB | 2 GB | 10 GB | [Requisitos mínimos](https://www.debian.org/releases/trixie/amd64/ch03s04.en.html) |

Fecha de consulta: 22/11/2026

**Alta disponibilidad en Proxmox**: requiere al menos 3 nodos para mantener el quórum y almacenamiento compartido o replicado entre nodos.
