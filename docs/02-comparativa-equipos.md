# Comparativa: equipo de clase vs. equipo de casa

Comparación de los dos equipos en los que se ejecuta el entorno de pruebas en VirtualBox, identificación de posibles **cuellos de botella** y propuestas para reducirlos.

Los datos salen de los inventarios de cada equipo (`inventario-portatil-clase.md` e `inventario-torre-casa.md`). Las características del procesador del portátil que no aparecen en su informe se han tomado de la ficha oficial de Intel.

---

## 1. Tabla comparativa

| Componente | Portátil (clase) | Torre (casa) | Mejor para el laboratorio |
|---|---|---|---|
| **Procesador** | Intel Core Ultra 5 125H | AMD Ryzen 7 5800X | Torre |
| Núcleos | 14 (4 de rendimiento + 8 de eficiencia + 2 de bajo consumo) | 8 (todos iguales) | — |
| Hilos | 18 | 16 | Portátil (por número) |
| Frecuencia máxima | 4,5 GHz | 4,85 GHz | Torre |
| Tipo de equipo | Portátil (consumo limitado) | Sobremesa | Torre |
| Virtualización | Intel VT-x *(N/C: confirmar)* | AMD-V | Empate |
| **RAM** | 32 GB (30 GiB visibles) | 32 GB DDR4-3600 (2 × 16 GB) | Empate |
| Ampliación de RAM | N/C (máximo del procesador: 96 GB) | 2 ranuras libres, hasta 128 GB | Torre |
| RAM usada en reposo | **10 GiB** | 3,4 GiB | Torre |
| **Disco del sistema y de las VM** | Disco de clase (SSD portátil USB) | Disco de clase (SSD portátil USB) | Empate (mismo disco) |
| Espacio libre en `/home` | 199 GB | 199 GB | Empate (mismo disco) |
| Disco interno rápido | NVMe (con Windows, sin usar) | Samsung 990 PRO NVMe 2 TB (otros sistemas) | Torre |
| Disco adicional | — | Toshiba HDD 2 TB, libre | Torre |
| **Gráfica** | Intel Arc (integrada) | NVIDIA GTX 960 | Torre |
| **Red** | Ethernet + Wi-Fi conectados | Solo Wi-Fi (Ethernet sin cable) | Portátil |
| **CPU en reposo** | 98,25 % inactiva | 99,61 % inactiva | Empate |
| Espera de E/S en reposo | 0,55 % | 0,04 % | Torre |

---

## 2. Cuellos de botella

### 2.1. RAM: limita las máquinas virtuales que pueden funcionar a la vez (los dos equipos)

- Los dos equipos tienen **32 GB**. El entorno completo necesita unos **49 GB** si se arranca todo a la vez (los tres nodos de Proxmox suman 30 GB por sí solos).
- En el **portátil** el problema es mayor: en reposo ya hay **10 GiB ocupados**, frente a los 3,4 GiB de la torre. Quedan unos 20 GiB útiles para las VM.

**Soluciones**
- Trabajar por **escenarios**, arrancando solo las máquinas que hacen falta para cada prueba.
- En la práctica, reducir los recursos de los nodos de Proxmox, como se explica en `mi-configuracion.md` («Teórico vs. práctico»).
- **Portátil**: revisar qué procesos ocupan la memoria en reposo (`ps aux --sort=-%mem | head`) y cerrar lo que no haga falta (contenedores de Docker, navegador…).
- **Torre**: añadir 2 × 16 GB DDR4-3600 iguales en las ranuras libres → **64 GB**, suficiente para arrancar el clúster completo.

### 2.2. Disco de clase por USB: todas las máquinas virtuales comparten un solo disco externo (los dos equipos)

- El sistema y todos los discos virtuales están en un **SSD portátil conectado por USB**. Cuando varias VM leen y escriben a la vez, el puerto USB y el disco son el punto más lento.
- En la torre hay un **NVMe Samsung 990 PRO** mucho más rápido que no se usa para el laboratorio.

**Soluciones**
- Conectar el disco siempre al puerto **USB más rápido** (USB-C / USB 3.2) y no a puertos USB 2.0 ni a concentradores.
- No arrancar a la vez varias máquinas que usen mucho el disco (por ejemplo, TrueNAS haciendo copias y Proxmox migrando una VM).
- **Torre**: mover a un disco interno las máquinas virtuales que más trabajan con el disco. Las copias de seguridad y las VM poco exigentes pueden ir al **HDD Toshiba de 2 TB**, que está libre.

### 2.3. Espacio en disco: los discos dinámicos pueden llenar el disco de clase (los dos equipos)

- Quedan **199 GB libres** en `/home`. Los discos de las VM son dinámicos y crecen con el uso. Sus tamaños máximos suman mucho más que el espacio disponible: solo los tres nodos de Proxmox llegan a unos 2,9 TB.
- Si el disco se llena, las VM se pausan o se corrompen sus discos.

**Soluciones**
- Vigilar el espacio libre (`df -h /home`) y borrar las instantáneas que ya no se necesiten.
- Quitar los discos que solo eran de prueba, como el de 80 GB de cada nodo de Proxmox.
- Para la práctica, usar discos más pequeños en los nodos de Proxmox y dejar los tamaños grandes como diseño teórico.
- **Torre**: guardar las copias y las VM grandes en el HDD de 2 TB.

### 2.4. Procesador del portátil: núcleos de distintos tipos y consumo limitado

- De los 14 núcleos del Core Ultra 5 125H, solo 4 son de rendimiento. El resto son de eficiencia y de bajo consumo, más lentos. Una vCPU de VirtualBox puede acabar en un núcleo lento.
- Al ser un portátil, con carga sostenida baja la frecuencia para no calentarse ni gastar demasiado.
- La torre tiene 8 núcleos iguales y una refrigeración de sobremesa: rinde mejor con carga continua.

**Soluciones**
- No asignar más de 4 vCPU a una sola VM en el portátil.
- Trabajar con el **cargador conectado** y el perfil de energía en **«Rendimiento»**.
- Hacer las pruebas de carga pesada (clúster, migraciones) en la torre.

### 2.5. Red de la torre: solo Wi-Fi

- La torre solo está conectada por Wi-Fi. No afecta a la red interna de las VM, que va por dentro de VirtualBox, pero sí a las descargas de ISO y a las actualizaciones.

**Solución**
- Conectar el cable Ethernet cuando haya que descargar ISO grandes o actualizar muchas máquinas.

---

## 3. Conclusión

| Uso | Equipo recomendado |
|---|---|
| Trabajar en clase, configurar máquinas sueltas, pruebas ligeras | **Portátil** |
| Clúster de Proxmox, migraciones, pruebas de carga, muchas VM a la vez | **Torre** |

- La **torre** es mejor para el laboratorio: CPU de sobremesa con más rendimiento sostenido, menos RAM ocupada en reposo, RAM ampliable a 128 GB y dos discos internos que se pueden aprovechar.
- El **portátil** permite llevar el entorno a clase, pero su RAM ocupada en reposo y su procesador de bajo consumo limitan cuántas VM pueden funcionar a la vez.
- El cuello de botella **común a los dos** es el **disco de clase por USB**, tanto por velocidad como por espacio. Es el primer punto que hay que vigilar cuando el entorno vaya lento.

---

## Fuentes

Fecha de consulta: 22/11/2026

| Dato | Fuente |
|---|---|
| Intel Core Ultra 5 125H: especificaciones | <https://www.intel.com/content/www/us/en/products/sku/236848/intel-core-ultra-5-processor-125h-18m-cache-up-to-4-50-ghz/specifications.html> |
| Datos de cada equipo | Informes del sistema (`uptime`, `lscpu`, `free`, `df`, `lsblk`, `ip`, `vmstat`, `mpstat`, `iostat`, `dmidecode`, `smartctl`, `lspci`) |
