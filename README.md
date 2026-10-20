# DevLab Infrastructure — Automated Cloud & Container Environment

Un entorno de infraestructura web y base de datos relacional local, automatizado y desplegado mediante virtualización ligera, contenedores y scripts de aprovisionamiento autogestionados.

---

## Resumen del Proyecto

Este proyecto simula un entorno servidor de producción moderno. Utiliza **Vagrant** y **VirtualBox** para aprovisionar una máquina virtual **Ubuntu 22.04 LTS (Jammy Jellyfish)**. 

A través del aprovisionamiento en línea (`inline provisioner`) de Vagrant, la infraestructura genera internamente todos los archivos de configuración (`index.html`, `init.sql` y `docker-compose.yml`) e inicia automáticamente la orquestación con **Docker Compose V2**, eliminando la necesidad de dependencias externas o configuraciones manuales.

---

## ¿Cómo Funciona la Automatización?

El laboratorio utiliza un enfoque autocontenido en el `Vagrantfile`:

1. **Aprovisionamiento Shell:** Al iniciar la VM, Vagrant despliega una máquina limpia con Ubuntu 22.04 LTS.
2. **Generación In-Situ:** Se crean de forma automática la interfaz web (`index.html`), la estructura inicial de la base de datos (`init.sql`) y la orquestación (`docker-compose.yml`).
3. **Orquestación con Docker:** Vagrant invoca **Docker Compose V2** para levantar el servidor web Apache2 y la base de datos MySQL con volúmenes persistentes.

---

## Arquitectura del Sistema

```text
[ Windows Host (Navegador / HeidiSQL / DBeaver) ]
                         │
               (Forwarded Ports: 8080 / 3306)
                         │
┌────────────────────────┴────────────────────────┐
│ VirtualBox VM: DevLab-Server (Ubuntu 22.04 LTS) │
│                                                 │
│   ┌─────────────────────────────────────────┐   │
│   │           Docker Engine V2              │   │
│   │                                         │   │
│   │  ┌─────────────────┐ ┌───────────────┐  │   │
│   │  │ Apache2 Web     │ │ MySQL 8.0 DB  │  │   │
│   │  │ (httpd:alpine)  │ │ (Volumen Persistent)│  │
│   │  └────────┬────────┘ └───────┬───────┘  │   │
│   └───────────┼──────────────────┼──────────┘   │
└───────────────┼──────────────────┼──────────────┘
                ▼                  ▼
          [ Portal Web ]    [ db_data (Volume) ]