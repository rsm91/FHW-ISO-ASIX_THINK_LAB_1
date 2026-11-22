# Inventario: equipo de casa

Inventario obtenido a partir del informe del sistema generado el **22/11/2026 a las 18:16** y de los datos de hardware recogidos a las **18:24**.

## Datos generales

| Dato | Valor |
|---|---|
| Equipo | Torre AMD (casa) |
| Hostname | `canvia-al-teu-nom` |
| Sistema operativo | GNU/Linux, kernel `6.8.0-139-generic` |
| Tiempo encendido | 2 h 34 min |
| Carga media (1, 5, 15 min) | 0,16 · 0,21 · 0,24 |

## Placa base y firmware

| Dato | Valor |
|---|---|
| Placa base | Gigabyte X570S AORUS ELITE AX |
| BIOS / UEFI | American Megatrends, versión F7c (20/09/2023) |
| Modo de arranque | UEFI (partición EFI montada en `/boot/efi`) |

## Procesador

| Dato | Valor |
|---|---|
| Modelo | AMD Ryzen 7 5800X |
| Núcleos físicos | 8 |
| Hilos por núcleo | 2 |
| CPU lógicas | 16 |
| Frecuencia máxima | 4.851 MHz |
| Sockets | 1 |
| Virtualización | **AMD-V** (indicadores `svm` y `npt` presentes) |

## Memoria

| Dato | Valor |
|---|---|
| RAM total | 32 GB (31 GiB visibles) |
| Módulos | 2 × 16 GB DDR4 a 3.600 MT/s |
| Referencia | F4-3600C16-16GVKC (G.Skill) |
| Ranuras | 4 en total: **2 ocupadas, 2 libres** |
| Capacidad máxima de la placa | 128 GB |
| Corrección de errores (ECC) | No |
| Uso en reposo | 3,4 GiB usados · 27 GiB disponibles |
| Swap | zram (comprimida en RAM), sin uso |

## Almacenamiento

| Disco | Modelo | Tipo | Conexión | Tamaño | Uso | Salud (SMART) |
|---|---|---|---|---|---|---|
| **sdb** | PSSD X31 | SSD portátil | **USB** | 953,9 GB | Disco de clase: sistema y `/home` | N/C |
| **nvme0n1** | Samsung SSD 990 PRO 2TB | SSD NVMe | PCIe (NVMe 2.0) | 2 TB | Otros sistemas (Windows, Linux en LVM, datos) · 155 GB usados | PASSED |
| **sda** | Toshiba HDWD120 | **Disco mecánico (HDD)** | SATA | 2 TB | Sin particiones, sin usar | N/C |
| **zram0** | — | Swap en RAM | — | 7,8 GB | Swap | — |

**Particiones del disco de clase (sdb):**

| Partición | Sistema de ficheros | Tamaño | Usado | Libre | Uso | Montaje |
|---|---|---|---|---|---|---|
| sdb1 | vfat (FAT32) | 508 MB | 17 MB | 492 MB | 4 % | `/boot/efi` |
| sdb2 | ext4 | 124 GB | 29 GB | 89 GB | 25 % | `/` |
| sdb3 | ext4 | 814 GB | 575 GB | 199 GB | 75 % | `/home` |

**Particiones del NVMe (nvme0n1)**, ninguna montada en esta sesión: vfat (EFI), reservada de Windows, NTFS (Windows), ext4, XFS, NTFS «datos» y LVM (`rl-root`, `rl-home`, `rl-swap`, probablemente Rocky Linux).

- El sistema arranca desde **sdb**, que es el **mismo disco de clase** que en el portátil: sus particiones tienen los mismos UUID.

## Tarjeta gráfica

| Dato | Valor |
|---|---|
| Modelo | NVIDIA GeForce GTX 960 (GM206) |

## Red

| Interfaz | Tipo | Estado | Dirección IPv4 | Errores | Descartes |
|---|---|---|---|---|---|
| `enp5s0` | Ethernet (cable) | DOWN (sin cable) | — | 0 | 0 |
| `wlp6s0` | Wi-Fi | UP | 192.168.1.102/24 | 0 | 0 |
| `docker0` | Puente de Docker | DOWN | 172.17.0.1/16 | 0 | 0 |
| `vboxnet0` | Solo-anfitrión de VirtualBox | DOWN | — | 0 | 0 |

## Estado en reposo (línea base)

| Recurso | Medida | Resultado |
|---|---|---|
| CPU | `mpstat` (media de 5 s) | 99,61 % inactiva |
| CPU: espera de E/S | `mpstat` | 0,04 % |
| Memoria | `vmstat` | Sin uso de swap (si/so = 0) |
| Disco sdb | `iostat` | 1,09 % de utilización; espera de escritura 1,15 ms |
| Disco nvme0n1 | `iostat` | Prácticamente sin actividad |

## Observaciones

- **Hostname sin personalizar**: el equipo sigue llamándose `canvia-al-teu-nom`. Hay que cambiarlo.
- **El disco de clase va por USB**: es un SSD portátil, pero todo el sistema y las máquinas virtuales dependen del puerto USB. El NVMe interno es mucho más rápido y no se usa para el entorno de pruebas.
- **Memoria ampliable**: quedan 2 ranuras libres y la placa admite hasta 128 GB. Añadir otros 2 × 16 GB iguales llevaría el equipo a 64 GB, lo que permitiría arrancar el clúster de Proxmox completo.
- **Disco mecánico sin usar**: el Toshiba de 2 TB está libre. Sirve para guardar copias de seguridad o máquinas virtuales poco exigentes, pero al ser mecánico es lento para las que trabajan mucho con el disco.
- **Mismo disco, distinto kernel**: arranca el mismo disco que en clase, pero con el kernel `6.8.0-139` en lugar del `7.0.0-31`.
- **Conexión solo por Wi-Fi**: el puerto Ethernet está sin cable. Para descargar ISO grandes, el cable sería más rápido y estable.

## Datos pendientes (N/C)

| Dato | Cómo comprobarlo |
|---|---|
| Salud del disco de clase (sdb) | `sudo smartctl -H -i -d sat /dev/sdb` (el puente USB necesita la opción `-d sat`) |
| Salud del disco mecánico (sda) | `sudo smartctl -H -i /dev/sda` |
| Fuente de alimentación | Etiqueta de la fuente (inspección física) |
