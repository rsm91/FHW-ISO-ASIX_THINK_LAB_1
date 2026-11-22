# Inventario: equipo de clase

Inventario obtenido a partir del informe del sistema generado el **20/11/2026 a las 19:21**.

## Datos generales

| Dato | Valor |
|---|---|
| Equipo | Portátil ASUS (clase) |
| Hostname | `canvia-al-teu-nom` |
| Sistema operativo | GNU/Linux, kernel `7.0.0-31-generic` |
| Firmware | UEFI (partición EFI montada en `/boot/efi`) |
| Tiempo encendido | 1 h 54 min |
| Carga media (1, 5, 15 min) | 0,70 · 0,67 · 0,63 |

## Procesador

| Dato | Valor |
|---|---|
| Modelo | Intel Core Ultra 5 125H |
| CPU lógicas | 18 |
| Sockets | 1 |
| Nodo NUMA | 1 (CPU 0–17) |
| Frecuencia en el momento del informe | 47 % de la máxima |

## Memoria

| Dato | Valor |
|---|---|
| RAM total | 30 GiB |
| En uso | 10 GiB |
| Libre | 17 GiB |
| Búfer / caché | 4,5 GiB |
| Disponible | 20 GiB |
| Swap | 15 GiB (zram, comprimida en RAM), sin uso |

## Almacenamiento

| Disco | Partición | Sistema de ficheros | Tamaño | Usado | Libre | Uso | Montaje |
|---|---|---|---|---|---|---|---|
| **sda** (≈ 1 TB) | sda1 | vfat (FAT32) | 508 MB | 17 MB | 492 MB | 4 % | `/boot/efi` |
| | sda2 | ext4 | 124 GB | 29 GB | 89 GB | 25 % | `/` |
| | sda3 | ext4 | 814 GB | 574 GB | 199 GB | 75 % | `/home` |
| **nvme0n1** (interno) | p1 | vfat (SYSTEM) | — | — | — | — | No montado |
| | p2 | — (reservada de Windows) | — | — | — | — | No montado |
| | p3 | NTFS (OS) | — | — | — | — | No montado |
| | p4 | NTFS | — | — | — | — | No montado |
| | p5 | vfat (MYASUS) | — | — | — | — | No montado |
| | p6 | ext4 (chboot) | — | — | — | — | No montado |
| | p7 | ext4 | — | — | — | — | No montado |
| **zram0** | — | swap | 15 GB | 0 | — | — | `[SWAP]` |

- El sistema arranca desde **sda**, que es el disco de clase.
- El disco interno **nvme0n1** tiene la instalación de Windows del fabricante (particiones OS, SYSTEM y MYASUS de recuperación). En esta sesión no se usa.

## Red

| Interfaz | Tipo | Estado | Dirección IPv4 | Errores | Descartes |
|---|---|---|---|---|---|
| `eno1` | Ethernet (cable) | UP | 192.168.40.122/24 | 0 | 8 en transmisión |
| `wlp1s0` | Wi-Fi | UP | 10.96.218.52/24 | 0 | 0 |
| `docker0` | Puente de Docker | DOWN | 172.17.0.1/16 | 0 | 798 en transmisión |
| `vboxnet0` | Solo-anfitrión de VirtualBox | DOWN | — | 0 | 0 |

## Estado en reposo (línea base)

| Recurso | Medida | Resultado |
|---|---|---|
| CPU | `mpstat` (media de 5 s) | 98,25 % inactiva |
| CPU: espera de E/S | `mpstat` | 0,55 % |
| Memoria | `vmstat` | Sin uso de swap (si/so = 0) |
| Disco sda | `iostat` | 1,68 % de utilización; espera de escritura 1,29 ms |
| Disco nvme0n1 | `iostat` | Prácticamente sin actividad |

## Observaciones

- **Hostname sin personalizar**: el equipo sigue llamándose `canvia-al-teu-nom` («cambia a tu nombre»). Hay que cambiarlo.
- **Memoria en uso en reposo**: 10 GiB ocupados sin carga. Conviene revisar qué procesos los usan (`ps aux --sort=-%mem | head`).
- **Espacio en `/home`**: quedan 199 GB libres (75 % de uso). Los discos dinámicos de las máquinas virtuales crecen con el uso. Por ejemplo, los tres nodos de Proxmox suman un máximo de más de 2,5 TB si se llenaran.
- **Dos conexiones activas a la vez**: cable y Wi-Fi están conectados al mismo tiempo, en redes distintas.

## Datos pendientes (N/C)

| Dato | Cómo comprobarlo |
|---|---|
| Núcleos físicos (rendimiento / eficiencia) | `lscpu` completo |
| Soporte de virtualización (VT-x) | `lscpu \| grep -i virtual` |
| Tipo, velocidad y ranuras de RAM | `sudo dmidecode -t memory` |
| Modelo, tipo y conexión de los discos (SSD/HDD, SATA/USB/NVMe) | `lsblk -d -o NAME,MODEL,TRAN,ROTA,SIZE` |
| Estado de salud de los discos | `sudo smartctl -a /dev/sda` |
| Tarjeta gráfica | `lspci \| grep -i vga` |
| Versión de BIOS/UEFI | `sudo dmidecode -t bios` |
| Batería | `upower -i $(upower -e \| grep BAT)` |
