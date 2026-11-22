# Pruebas de diagnóstico y saturación: CPU, memoria, disco y red

Pruebas de diagnóstico y saturación realizadas en el **equipo de casa (torre AMD)**, siguiendo la práctica **M2-P1 «Saturació i diagnòstic»**.

**Objetivo**: generar de forma **controlada** carga sobre cada recurso, observar las métricas y distinguir entre **síntoma, métrica, causa y acción correctora**. Con los datos se comprueba si se confirman los cuellos de botella de `comparativa-equipos.md`.

Por cada prueba se registran la **herramienta**, las **condiciones**, el **resultado** y la **interpretación**. Antes de cada prueba se anotó una **previsión**.

---

## 1. Normas de seguridad

- ☐ Las pruebas se hacen sobre un **equipo propio** y sin datos importantes.
- ☐ Las pruebas de **red** se hacen solo en la **red interna de VirtualBox**. **Nunca** contra Internet ni contra servidores de terceros.
- ☐ Antes de las pruebas de disco, comprobar el espacio libre (`df -h`) y dejar margen.
- ☐ Memoria: empezar con un **60–70 %** de la RAM disponible y pruebas cortas.
- ☐ Cerrar el navegador y las máquinas virtuales que no intervengan en la prueba.

---

## 2. Preparación

```bash
sudo apt update
sudo apt install -y stress-ng sysstat iotop fio iperf3 htop btop procps psmisc memtester smartmontools
```

| Dato | Valor |
|---|---|
| Equipo | Torre AMD Ryzen 7 5800X · 32 GB DDR4 |
| Disco probado | Disco de clase `/dev/sdb` (SSD NVMe en caja USB) |
| Interfaz de red física | `wlp6s0` (Wi-Fi) |
| Monitorización visual | `btop` y Monitor del sistema de GNOME |

---

## 3. Línea base (estado en reposo)

Sin un «antes» no se puede interpretar el «durant». La línea base se tomó con los comandos de la práctica (`uptime`, `lscpu`, `free`, `df`, `lsblk`, `ip`, `vmstat`, `mpstat`, `iostat`) y con `btop`.

| Recurso | Valor base | Observación |
|---|---|---|
| CPU inactiva (%) | 99,61 % (btop: 2 % de uso, 33 °C) | En reposo |
| Carga media (1 min) | 0,16 | |
| RAM disponible | 27 GiB de 31 GiB (24,7 GiB con navegador abierto) | |
| Swap usada | 0 | Sin presión de memoria |
| Ocupación del disco (`/home`) | 75 % (575 GB usados) | Subió a 617 GB en una hora por el crecimiento de los discos de las VM |
| Disco: espera de escritura / %util | 1,15 ms / 1,09 % | Disco descansado |
| Red: errores | 0 | |
| Espera de E/S de la CPU | 0,04 % | |

---

## 4. Prueba A · CPU

**Objetivo**: saturar la CPU y distinguir entre uso global, uso por núcleo y proceso responsable.

```bash
# Observación (otra terminal): btop o mpstat -P ALL 1
stress-ng --cpu 1 --cpu-method loop --timeout 60s --metrics-brief        # A1: un núcleo
stress-ng --cpu 0 --cpu-method matrixprod --timeout 60s --metrics-brief  # A2: todos
```

**Qué observar**: en A1 el uso **global** apenas sube, pero **una** CPU lógica llega casi al 100 %. En A2 `%idle` cae cerca de 0: el sistema está limitado por CPU.

---

## 5. Prueba B · Memoria

```bash
sudo memtester 2G 1                                                       # B1: errores
# Observación (otra terminal): btop, o watch -n 1 free -h y vmstat 1
stress-ng --vm 1 --vm-bytes 70% --vm-keep --timeout 90s --metrics-brief   # B2: presión
```

**Qué observar**: en B1, todas las pruebas en `ok`. En B2, cuánto baja la memoria *available* y si aparece swap (columnas `si`/`so` de `vmstat`). Que no haya errores en el registro **no** significa que no haya presión: lo demuestran las métricas.

---

## 6. Prueba D · Disco

```bash
sudo smartctl -H -d sntasmedia /dev/sdb      # D0: salud (puente USB-NVMe ASMedia)

# Observación (otra terminal): iostat -xz 1
mkdir -p ~/fio
fio --name=seqwrite --filename=$HOME/fio/test.bin --size=2G --rw=write --bs=1M --iodepth=16 \
    --numjobs=1 --direct=1 --runtime=60 --time_based --group_reporting | tee ~/fio/D1-seqwrite.txt
fio --name=randrw --filename=$HOME/fio/test.bin --size=2G --rw=randrw --rwmixread=70 --bs=4k \
    --iodepth=32 --numjobs=4 --direct=1 --runtime=60 --time_based --group_reporting | tee ~/fio/D2-randrw.txt
rm -f ~/fio/test.bin
```

> **Nota**: sin `--ioengine`, fio usa el motor síncrono `psync` y limita la cola a 1 por proceso. En D2, con 4 procesos, la cola efectiva es de 4.

**Qué observar en `iostat`**: `%util` cerca del 100 % (disco ocupado todo el tiempo), `await` (latencia), `aqu-sz` (cola) y `r/s`, `w/s` (operaciones por segundo).

> **Concepto clave**: «disco lleno» es un problema de **capacidad**; «disco saturado» es un problema de **rendimiento**.

---

## 7. Prueba E · Red

**Red interna de VirtualBox** (aislada, 192.168.50.0/24):

| Papel | Máquina | IP |
|---|---|---|
| Objetivo (servidor) | Cliente Debian | 192.168.50.128 |
| Generador (cliente) | debianCliente | 192.168.50.135 |

```bash
iperf3 -s                                  # en el objetivo
iperf3 -c 192.168.50.128 -P 8 -t 60        # en el generador
ping -i 0.2 192.168.50.128                 # en el generador, a la vez: efecto sobre la latencia
```

**Red física del host** (medidas no agresivas):

```bash
iw dev wlp6s0 link | grep -E "signal|bitrate"
ping -c 20 $(ip route | awk '/default/ {print $3; exit}')
```

**Qué observar**: una saturación de red puede **no generar ningún error** en los registros. Los contadores, las retransmisiones y la latencia dan más información. En una red virtual, el límite suele ser la **CPU del host**.

---

## 8. Resultados

| Prueba | Herramienta | Previsión | Resultado | Interpretación |
|---|---|---|---|---|
| A1 · CPU, 1 núcleo | `stress-ng --cpu 1` | Una CPU lógica al 100 %, global cerca del 6 % | 60 s reales con 59,99 s de CPU de usuario · 259.912 bogo ops · **4.331,87 bogo ops/s** · 0 fallos | El worker ocupa una CPU lógica al 100 % todo el tiempo (tiempo de usuario ≈ tiempo real). Con 16 CPU lógicas, el uso global ronda el 6 %: el equipo no se nota cargado aunque un núcleo esté saturado. **Se cumple la previsión** |
| A2 · CPU, todos | `stress-ng --cpu 0` | `%idle` ≈ 0; frecuencia estable (sobremesa) | 16 workers · 60 s reales con 950,89 s de usuario + 0,47 s de sistema · 686.016 bogo ops · **11.433,06 bogo ops/s** · 0 fallos | 951,36 s de CPU de 960 s posibles (16 × 60 s) = **CPU al 99 %**: el sistema queda limitado por CPU y lo aguanta sin fallos. El método (`matrixprod`) es distinto al de A1 (`loop`), así que sus bogo ops no se comparan entre sí. **Se cumple la previsión** |
| B1 · Errores de RAM | `memtester` | Sin errores | memtester 4.6.0 · 2048 MB reservados y bloqueados (`mlock`) · 1 pasada · **18 de 18 pruebas en `ok`** | No hay errores en los 2 GB probados. Solo cubre esa parte de la RAM: para descartar fallos en los 32 GB hace falta Memtest86+ desde el arranque. **Se cumple la previsión** |
| B2 · Presión de RAM | `stress-ng --vm 70%` + `btop` | Baja *available*; sin swap | 1 worker · 90,71 s reales · 16.719.023 bogo ops · **184.309,63 bogo ops/s** · 0 fallos · **btop durante la prueba**: proceso `stress-ng-vm` con **15 GB**; RAM usada de 6,48 → **19,7 GiB**; *available* de 24,7 → **11,4 GiB**; **swap: 0 B**; CPU total 7 %; temperatura de 33 → 69 °C | La memoria disponible baja 13,3 GiB, pero quedan 11,4 GiB libres y **no se usa swap** ni hay paginación: **hay presión, pero no falta de memoria**. Confirma el presupuesto de RAM: la torre puede con escenarios de hasta unos 23 GB de VM. La subida de temperatura se debe al núcleo que recorre la memoria a máxima frecuencia. **Se cumple la previsión** |
| D0 · Salud del disco | `smartctl -H -d sntasmedia /dev/sdb` | PASSED | **PASSED**. Con `-d sat` no funcionaba: el puente USB (ID `152e:7001`) necesita el tipo específico de puente USB-NVMe | El disco de clase está **sano** según su autodiagnóstico, y coincide con los 0 errores de E/S de D1 y D2. Que `-d sat` fallara indica que dentro de la caja USB hay un SSD **NVMe**, no SATA. **Se cumple la previsión** |
| D1 · Escritura secuencial | `fio` (bloques de 1 MB, 60 s) | 400–900 MB/s si está en USB 3.2 | **425 MiB/s (445 MB/s)** · 424 IOPS · 24,9 GiB escritos · latencia media 2,35 ms (p99: 6,78 ms; máx.: 22,4 ms) · rendimiento entre 92 y 754 MiB/s según el momento · **`%util` del disco: 97,4 %** · 0 errores | El disco de clase queda **saturado** (`%util` ≈ 100 %) a unos 445 MB/s, una cifra propia de un puerto USB 3.2 de 5 Gb/s. El rendimiento varía mucho (de 92 a 754 MiB/s), lo que indica caídas puntuales del SSD o del puente USB. Todas las VM tienen que repartirse estos ~445 MB/s. **Confirma el cuello de botella del disco por USB** |
| D2 · 4K aleatorio | `fio` (bloques de 4 KB, 70 % lectura, 4 procesos, cola efectiva de 4) | Entre 5.000 y 20.000 IOPS | **Lectura: 21.700 IOPS** (84,6 MiB/s, latencia media 150 µs, p99 338 µs) · **Escritura: 9.314 IOPS** (36,4 MiB/s, latencia media 80 µs) · **Total ≈ 31.000 IOPS / 121 MiB/s** · `%util`: 84,6 % · 0 errores | Supera la previsión: el SSD responde bien a operaciones pequeñas, con latencias por debajo de 0,2 ms de media, y no llega a saturarse. **El acceso aleatorio no es el punto débil**: el límite del disco de clase está en el rendimiento secuencial (instalaciones, copias, migraciones a la vez) y en el espacio |
| E · Red interna | `ping` + `iperf3 -P 8 -t 60` + `btop` / Monitor del sistema | Varios Gb/s, limitado por la CPU | **Reposo**: ping 0 % de pérdida, media 0,288 ms · **Saturación (8 flujos, 60 s)**: **41,2 GB a 5,89 Gbit/s** (≈ 713–752 Mbit/s por flujo) · **757.267 retransmisiones TCP** · ping durante la prueba: 0,19–0,28 ms, sin pérdidas · host: una CPU lógica al **83 %**, resto por debajo del 10 % · RAM del host 17,4 GB (52 %), swap 0 | La red interna llega a unos **5,9 Gbit/s**, muy por encima de una red Gigabit física. El límite es la **CPU del host**: VirtualBox mueve los paquetes con un hilo que deja una CPU lógica casi saturada. Las retransmisiones indican que el conmutador virtual descarta paquetes cuando se satura. La **latencia no empeora** porque el resto de CPU está libre: sin un enlace limitado, el ping se mantiene estable. **Se cumple la previsión** |
| E · Red física | `iw` / `ping` (20 paquetes a la puerta de enlace) | Wi-Fi: 1–5 ms, con más variación | Señal **−45 dBm** · enlace de 300 Mbit/s (recepción) y 240 Mbit/s (transmisión), Wi-Fi 4 a 40 MHz · ping: **0 % de pérdida**, mínimo 0,77 ms, **media 19,8 ms**, máximo 75,6 ms, desviación 17,7 ms | La señal es excelente, así que no es un problema de cobertura. Pero la latencia es **muy irregular**: alterna respuestas de ~1 ms con otras de ~21 ms y picos de 75 ms, un patrón típico del **ahorro de energía de la tarjeta Wi-Fi**. El enlace (máximo 300 Mbit/s) queda muy por debajo de un cable Gigabit. **Confirma el cuello de botella de la red de la torre** |

---

## 9. Conclusión: ¿se confirman los cuellos de botella?

| Cuello de botella (comparativa) | Prueba | ¿Se confirma? | Dato que lo demuestra |
|---|---|---|---|
| Disco de clase por USB: rendimiento | D1, D2 | ✅ **Sí, matizado** | Escritura secuencial saturada a ~445 MB/s (`%util` 97,4 %) con caídas a 92 MiB/s. En acceso aleatorio, en cambio, responde bien (31.000 IOPS, < 0,2 ms) |
| Disco de clase: espacio | Línea base / btop | ✅ **Sí** | `/home` pasó de 575 a 617 GB usados en una hora por el crecimiento de los discos dinámicos de las VM |
| RAM de la torre para el laboratorio | B2 | ❌ **No es un cuello de botella** para escenarios de hasta ~23 GB | Con 15 GB ocupados por la prueba quedan 11,4 GiB disponibles y la swap sigue a 0 |
| CPU de la torre | A2 | ❌ **No es un cuello de botella** | Aguanta el 99 % de uso en los 16 hilos sin fallos |
| Red interna de VirtualBox | E | ⚠️ **Limitada por la CPU del host** | 5,89 Gbit/s con una CPU lógica al 83 %; más que suficiente para el laboratorio |
| Red física de la torre (Wi-Fi) | E | ✅ **Sí** | Latencia media de 19,8 ms con picos de 75 ms y enlace de 300 Mbit/s |

**Acciones correctoras propuestas**
1. **Disco**: no lanzar a la vez tareas secuenciales pesadas (instalaciones, copias de TrueNAS, migraciones de Proxmox). Conectar el disco al puerto USB más rápido. Llevar copias y VM grandes al HDD de 2 TB.
2. **Espacio**: vigilar `/home` con una alerta al 80 % y reducir los discos de práctica.
3. **Wi-Fi**: desactivar el ahorro de energía (`sudo iw dev wlp6s0 set power_save off`) y volver a medir, o usar cable Ethernet para descargas grandes.

---

## Fuentes

| Tema | Fuente |
|---|---|
| Procedimiento de saturación y diagnóstico | Práctica ASIX ThinkLab M2-P1 «Saturació i diagnòstic» (CIP FP Batoi, 2026-2027) |
| Línea base | Informe del sistema de la torre (22/11/2026) |
| Evidencias | Capturas de pantalla y salidas de `fio` en `evidencies/tasca2/` |
