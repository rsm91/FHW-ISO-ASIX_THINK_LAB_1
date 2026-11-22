# FHW-ISO-ASIX ThinkLab 1

**Naix un sistema: instal·la, configura i vigila'l**: parte individual

Repositorio personal de la actividad **ASIX ThinkLab** de 1.º de ASIX, de los módulos de **Fonaments de Maquinari (FDM)** e **Implantació de Sistemes Operatius (ISO)**.

Contiene la documentación del **entorno de pruebas individual**, montado en **VirtualBox**, que simula el sistema informático de la empresa construido en grupo en el aula.

🔗 Repositorio: <https://github.com/rsm91/FHW-ISO-ASIX_THINK_LAB_1>
📋 Planificación (Trello): <https://trello.com/b/4rPa90u1/nace-un-sistema-parte-individual>

---

## Entorno de pruebas

| Función | Máquinas |
|---|---|
| Servidores de comunicaciones | OPNSense · MikroTik · DebianServidorComunicaciones (nftables) |
| Almacenamiento y copias | TrueNAS |
| Dominio | winServer (Windows Server 2025) |
| Monitorización | Zabbix (Debian 13) |
| Virtualización (clúster) | proxmoxn1 · proxmoxn2 · proxmoxn3 |
| Clientes | clientesWin11Pro · Cliente Debian (GNOME) |

- **Red interna (LAN)**: 192.168.50.0/24
- **Salida al exterior (WAN)**: adaptador NAT de VirtualBox en los servidores de comunicaciones

---

## Estructura del repositorio

```
FHW-ISO-ASIX_THINK_LAB_1/
├── README.md
├── docs/          ← documentación en Markdown, una carpeta por tarea
│   └── 00/
├── diagrames/     ← esquemas de red
├── evidencies/    ← capturas y registros, una subcarpeta por tarea
│   ├── tasca0/
│   ├── tasca2/
│   ├── tasca4/
│   ├── tasca5/
│   ├── tasca6/
│   ├── tasca7/
│   └── tasca8/
└── scripts/       ← scripts (copias de seguridad, VBoxManage…)
```

---

## Tareas individuales

| Tarea | Contenido | Documentación | Estado |
|---|---|---|---|
| **Tarea 0** | Montaje del entorno de pruebas | [docs/00](docs/00/) | ✅ Documentada |
| **Tarea 2** | Inventario del equipo de clase y de casa, cuellos de botella | — | ⏳ Pendiente |
| **Tarea 4** | Esquema de red del entorno en VirtualBox | — | ⏳ Pendiente |
| **Tarea 5** | Instalación y configuración de los sistemas | — | ⏳ Pendiente |
| **Tarea 6** | Creación de 3 tipos de RAID | — | ⏳ Pendiente |
| **Tarea 7** | Análisis de recursos del equipo físico | — | ⏳ Pendiente |
| **Tarea 8** | Caso práctico final | — | ⏳ Pendiente |

### Tarea 0: Montaje del entorno de pruebas

- [Requisitos recomendados](docs/00/requisitos-recomendados.md): requisitos oficiales de cada sistema y fuentes.
- [Configuración de red](docs/00/configuracion-red.md): red interna como LAN y NAT como salida al exterior, con esquema.
- [Mi configuración](docs/00/mi-configuracion.md): hardware virtual de cada máquina.

---

## Planificación

El seguimiento de las tareas individuales se hace en el tablero de Trello **[Nace un sistema — Parte INDIVIDUAL](https://trello.com/b/4rPa90u1/nace-un-sistema-parte-individual)**. Hay una tarjeta por tarea (T0–T8), con la consigna, el enunciado completo y una checklist.

| Lista | Significado |
|---|---|
| Por hacer | Tareas sin empezar |
| En curso | Tareas en las que se está trabajando |
| Revisión del profesorado | Tareas terminadas, pendientes de revisión |
| Hecho | Tareas revisadas y finalizadas |

---

## Herramientas

- **Trello**: planificación y seguimiento de las tareas
- **VirtualBox**: virtualización del entorno de pruebas
- **Markdown**: formato de la documentación
- **Git / GitHub**: control de versiones y entrega
