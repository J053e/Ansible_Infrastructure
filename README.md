# Infraestructura TI con Ansible y Podman

Proyecto de automatización y contenedorización de una infraestructura de servicios TI, con enfoque en **alta disponibilidad**, **seguridad** y **reproducibilidad**.

## Descripción

Este proyecto implementa una arquitectura donde:

- **GLPI** se despliega en contenedores con **Podman**
- **Wiki.js** se utiliza como plataforma de documentación técnica
- **HAProxy** publica los servicios hacia clientes
- **MariaDB Galera** provee alta disponibilidad en la capa de datos
- **MaxScale** centraliza el acceso a base de datos
- **Ansible** automatiza la configuración y operación de toda la infraestructura

## Arquitectura

La infraestructura final está compuesta por:

- `admin-nat` → acceso administrativo y salida/NAT
- `haproxy` → frontend público y reverse proxy
- `db1` y `db2` → clúster **MariaDB/Galera**
- `maxscale` → proxy de base de datos
- `ctr-host1` → host de contenedores (**GLPI** y **Wiki.js**)

## Servicios publicados

- **GLPI** → `http://172.24.133.164`
- **Wiki.js** → `http://wiki.lab`

> Ambos servicios comparten la misma IP pública y se diferencian por `Host header` en HAProxy.

## Tecnologías principales

- **Ansible**
- **Podman**
- **HAProxy**
- **MariaDB Galera**
- **MaxScale**
- **SELinux**
- **Firewalld**
- **Systemd**
- **Ansible Vault**
- **Podman Secret**

## Seguridad aplicada

- Credenciales cifradas con **Ansible Vault**
- Secretos de runtime manejados con **Podman Secret**
- **SELinux** activo y ajustado, no deshabilitado
- Servicios internos expuestos solo a través de **HAProxy**
- Segmentación de red entre administración, aplicación y base de datos

## Automatización

La infraestructura se organiza en roles de Ansible para mantener claridad y reusabilidad:

- `baseline`
- `admin_nat`
- `mariadb_galera`
- `mariadb_apps`
- `maxscale`
- `haproxy`
- `podman_glpi`
- `podman_wikijs`

## Objetivo del proyecto

Construir una plataforma de servicios TI que pueda:

- desplegarse de forma reproducible
- escalar con bajo acoplamiento
- mantener seguridad operativa
- centralizar documentación técnica en la propia infraestructura

## Estado final

- **GLPI** operativo detrás de HAProxy
- **Wiki.js** operativo y publicado
- **MaxScale** funcionando como punto único de acceso a base de datos
- **Ansible** reflejando la configuración final de la infraestructura

---

Proyecto académico orientado a **Infraestructura como Código**, **contenedores de aplicación** y **administración segura de servicios**.
