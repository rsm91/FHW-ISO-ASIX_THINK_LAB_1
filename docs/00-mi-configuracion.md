# Mi configuración

Configuración de las máquinas virtuales de mi entorno de pruebas individual en **VirtualBox**.

## OPNSense

- **Sistema**: OPNsense 26.7 (FreeBSD)
- **vCPU**: 1
- **RAM**: 3 GB
- **Disco**: 4 × 20 GB
- **Firmware**: BIOS
- **Chipset**: PIIX3
- **Controlador**: SATA
- **Adaptador 1**: NAT (WAN), Intel PRO/1000 MT Desktop
- **Adaptador 2**: Red interna (LAN), Intel PRO/1000 MT Desktop
- **Audio / USB**: desactivados
- **Otros**: —

## MikroTik

- **Sistema**: MikroTik RouterOS 7.23.8 (CHR, rama *long-term*, licencia Free)
- **vCPU**: 1
- **RAM**: 1 GB
- **Disco**: 2 GB
- **Firmware**: BIOS
- **Chipset**: PIIX3
- **Controlador**: SATA
- **Adaptador 1**: NAT (WAN), paravirtualizado (virtio-net)
- **Adaptador 2**: Red interna (LAN), paravirtualizado (virtio-net)
- **Audio / USB**: desactivados
- **Otros**: disco a partir de la imagen VDI oficial de CHR

## DebianServidorComunicaciones

- **Sistema**: Debian 13 «Trixie» con nftables, sin entorno gráfico
- **vCPU**: 1
- **RAM**: 1 GB
- **Disco**: 8 GB
- **Firmware**: BIOS
- **Chipset**: PIIX3
- **Controlador**: SATA
- **Adaptador 1**: NAT (WAN), Intel PRO/1000 MT Desktop
- **Adaptador 2**: Red interna (LAN), Intel PRO/1000 MT Desktop
- **Audio / USB**: desactivados
- **Otros**: —

## TrueNAS

- **Sistema**: TrueNAS 25.10 «Goldeye» Community Edition
- **vCPU**: 2
- **RAM**: 8 GB
- **Disco**: 7 × 40 GB (1 de arranque + 6 de datos)
- **Firmware**: EFI
- **Chipset**: PIIX3
- **Controlador**: SATA
- **Adaptador 1**: Red interna, Intel PRO/1000 MT Desktop
- **Adaptador 2**: —
- **Audio / USB**: desactivados
- **Otros**: —

## winServer

- **Sistema**: Windows Server 2025 Standard (Evaluation), con experiencia de escritorio
- **vCPU**: 2
- **RAM**: 4 GB
- **Disco**: 64 GB
- **Firmware**: EFI + TPM 2.0, sin Secure Boot
- **Chipset**: PIIX3
- **Controlador**: SATA
- **Adaptador 1**: Red interna, Intel PRO/1000 MT Desktop
- **Adaptador 2**: —
- **Audio / USB**: desactivados
- **Otros**: —

## Zabbix

- **Sistema**: Debian 13 «Trixie» sin entorno gráfico, con Zabbix 8.0 LTS
- **vCPU**: 2
- **RAM**: 2 GB
- **Disco**: 20 GB
- **Firmware**: BIOS
- **Chipset**: PIIX3
- **Controlador**: SATA
- **Adaptador 1**: Red interna, Intel PRO/1000 MT Desktop
- **Adaptador 2**: —
- **Audio / USB**: desactivados
- **Otros**: —

## proxmoxn1, proxmoxn2 y proxmoxn3

- **Sistema**: Proxmox VE 9.2 (basado en Debian 13)
- **vCPU**: 4
- **RAM**: 10 GB
- **Disco**: 100 GB (sistema) + 80 GB (pruebas de almacenamiento) + 6 × 130 GB (pool `vmdata` en RAIDZ2)
- **Firmware**: EFI
- **Chipset**: PIIX3
- **Controlador**: SATA
- **Adaptador 1**: Red interna, Intel PRO/1000 MT Server, modo promiscuo «Permitir todo»
- **Adaptador 2**: —
- **Audio / USB**: desactivados
- **Otros**: virtualización anidada VT-x/AMD-V activada

Los tres nodos son iguales para que, si uno falla, cualquiera de los otros pueda hacer de respaldo y arrancar sus máquinas virtuales (alta disponibilidad).

**Máquinas virtuales del clúster**

- **winServer**: Windows Server 2025 · 2 vCPU · 4 GB · 64 GB · nodo habitual proxmoxn1
- **debianServer**: Debian 13 · 2 vCPU · 2 GB · 20 GB · nodo habitual proxmoxn2
- **debianDatos**: Debian 13 · 2 vCPU · 2 GB · 20 GB + 100 GB de datos · nodo habitual proxmoxn3

**Teórico vs. práctico**

- **Teórico**: los tres nodos con 4 vCPU y 10 GB, para tener alta disponibilidad real.
- **Práctico**: para poder usarlo en el equipo anfitrión, proxmoxn2 y proxmoxn3 bajan a 1 vCPU, y proxmoxn1 se queda con los que necesiten las VM que se prueban (2–4). El clúster mantiene el quórum y permite probar la gestión conjunta, la migración y la replicación.
- **Limitación**: un nodo con 1 vCPU no puede arrancar una VM de 2 vCPU. Para migrar hacia esos nodos se usa una VM o un contenedor de 1 vCPU.

## clientesWin11Pro

- **Sistema**: Windows 11 Pro 26H2
- **vCPU**: 2
- **RAM**: 4 GB
- **Disco**: 80 GB
- **Firmware**: EFI + TPM 2.0, sin Secure Boot
- **Chipset**: PIIX3
- **Controlador**: SATA
- **Adaptador 1**: Red interna, Intel PRO/1000 MT Desktop
- **Adaptador 2**: —
- **Audio / USB**: desactivados
- **Otros**: —

## Cliente Debian

- **Sistema**: Debian 13 «Trixie» con escritorio GNOME
- **vCPU**: 2
- **RAM**: 2 GB
- **Disco**: 20 GB
- **Firmware**: BIOS
- **Chipset**: PIIX3
- **Controlador**: SATA
- **Adaptador 1**: Red interna, Intel PRO/1000 MT Desktop
- **Adaptador 2**: —
- **Audio / USB**: desactivados
- **Otros**: —
