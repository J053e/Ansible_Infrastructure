# Infraestructura TI Automatizada con Ansible, Podman, FreeIPA y Monitoreo Centralizado

Proyecto académico de **Infraestructura como Código** orientado al despliegue de una plataforma de servicios TI segura, reproducible y observable, integrando alta disponibilidad, contenedores, gestión centralizada de identidad y monitoreo operacional.

## Descripción

Este repositorio automatiza la configuración de una infraestructura basada en **Rocky Linux**, administrada con **Ansible** y desplegada sobre un mini-centro de datos virtual. La solución integra servicios web, base de datos en alta disponibilidad, publicación mediante reverse proxy, gestión centralizada de usuarios y una plataforma completa de monitoreo.

La infraestructura implementa:

* **GLPI** como sistema de gestión de documentación y servicios TI.
* **Wiki.js** como plataforma de documentación técnica interna.
* **HAProxy** como frontend público y reverse proxy.
* **MariaDB Galera** como clúster de base de datos en alta disponibilidad.
* **MaxScale** como proxy centralizado de acceso SQL.
* **FreeIPA** como sistema de identidad, DNS interno, Kerberos, LDAP, HBAC y sudo centralizado.
* **Prometheus, Grafana, Loki, Alloy, Alertmanager, Node Exporter y Blackbox Exporter** para monitoreo, logs y alertas.
* **Podman + systemd** para ejecución persistente de servicios contenedorizados.

## Arquitectura

La infraestructura está compuesta por los siguientes servidores:

| Host          | Rol                                                        |
| ------------- | ---------------------------------------------------------- |
| `admin-nat`   | Acceso administrativo, NAT y punto de entrada SSH          |
| `ipa1`        | FreeIPA, DNS interno, LDAP, Kerberos y políticas de acceso |
| `haproxy`     | Frontend público y reverse proxy                           |
| `db1` / `db2` | Clúster MariaDB Galera                                     |
| `maxscale`    | Proxy de base de datos                                     |
| `ctr-host1`   | Host Podman para GLPI y Wiki.js                            |
| `mon1`        | Plataforma central de monitoreo, logs y alertas            |

## Servicios publicados

| Servicio          | URL                                              |
| ----------------- | ------------------------------------------------ |
| GLPI              | `http://172.24.133.164`                          |
| Wiki.js           | `http://wiki.lab`                                |
| Grafana           | `http://localhost:3000` mediante túnel SSH       |
| Prometheus        | `http://localhost:9090` mediante túnel SSH       |
| Alertmanager      | `http://localhost:9093` mediante túnel SSH       |
| Blackbox Exporter | `http://localhost:9115` mediante túnel SSH       |
| Loki              | `http://localhost:3100/ready` mediante túnel SSH |

GLPI y Wiki.js comparten la misma IP pública y se diferencian mediante `Host header` en HAProxy.

## Funcionalidades principales

* Despliegue automatizado con Ansible.
* Contenedorización de servicios con Podman.
* Alta disponibilidad en base de datos con MariaDB Galera.
* Centralización del acceso SQL mediante MaxScale.
* Publicación de servicios mediante HAProxy.
* Gestión centralizada de usuarios y permisos con FreeIPA.
* DNS interno para el dominio `infra.lab`.
* Reglas HBAC para controlar acceso SSH por grupo y tipo de servidor.
* Reglas sudo centralizadas para administración segura.
* Monitoreo de hosts con Prometheus y Node Exporter.
* Dashboards operativos con Grafana.
* Centralización de logs con Loki y Alloy.
* Alertas automáticas con Alertmanager.
* Validación HTTP de GLPI y Wiki.js con Blackbox Exporter.
* Segmentación de red entre gestión, aplicación, base de datos y publicación.

## Tecnologías utilizadas

* **Ansible**
* **Ansible Vault**
* **Podman**
* **Systemd**
* **HAProxy**
* **MariaDB Galera**
* **MaxScale**
* **FreeIPA**
* **Kerberos / LDAP / SSSD**
* **Prometheus**
* **Grafana**
* **Loki**
* **Grafana Alloy**
* **Alertmanager**
* **Node Exporter**
* **Blackbox Exporter**
* **Firewalld**
* **SELinux**
* **Rocky Linux 9**

## Seguridad aplicada

El proyecto incorpora controles de seguridad a nivel de sistema, red, identidad y operación:

* Credenciales cifradas con **Ansible Vault**.
* Secretos de runtime manejados con **Podman Secret**.
* **SELinux** activo y ajustado para contenedores.
* Segmentación de red entre administración, aplicación y base de datos.
* Servicios internos no expuestos directamente al exterior.
* Acceso a interfaces administrativas mediante túnel SSH.
* DNS interno gestionado por FreeIPA.
* Autenticación centralizada con Kerberos/LDAP.
* Control de acceso con reglas HBAC por grupo y hostgroup.
* Reglas sudo centralizadas.
* Puerto `9100` de Node Exporter restringido únicamente al servidor de monitoreo.
* Límites de memoria y CPU para contenedores de monitoreo.
* Retención controlada de logs en Loki.

## Automatización

La infraestructura se organiza en roles de Ansible para facilitar mantenimiento, reutilización y despliegue por fases:

```text
baseline
admin_nat
freeipa_server
freeipa_client
freeipa_policy
mariadb_galera
mariadb_apps
maxscale
haproxy
podman_glpi
podman_wikijs
node_exporter
alloy_agent
monitoring_server
```

Ejemplo de ejecución:

```bash
ansible-playbook -i inventories/production/hosts.yml playbooks/site.yml \
  --vault-id infra@prompt \
  --tags monitoring_server \
  --limit mon1
```

## Monitoreo y observabilidad

La plataforma de monitoreo permite validar el estado completo de la infraestructura:

* Disponibilidad de servidores.
* Métricas de CPU, memoria, disco y red.
* Estado de servicios publicados.
* Logs centralizados por host y unidad systemd.
* Alertas automáticas por caída de hosts, alto consumo de recursos o servicios HTTP no disponibles.

Prometheus recolecta métricas de todos los hosts mediante Node Exporter, mientras que Blackbox Exporter valida la disponibilidad externa de GLPI y Wiki.js.

## Estado final

* GLPI operativo detrás de HAProxy.
* Wiki.js operativo y publicado.
* MariaDB Galera funcionando en alta disponibilidad.
* MaxScale funcionando como punto único de acceso a base de datos.
* FreeIPA operativo con DNS, usuarios, grupos, HBAC y sudo rules.
* Todos los hosts enrolados como clientes FreeIPA.
* Prometheus, Grafana, Loki, Alloy, Alertmanager, Node Exporter y Blackbox Exporter funcionales.
* Targets de monitoreo en estado `UP`.
* Acceso administrativo protegido mediante red de gestión y túneles SSH.

## Objetivo del proyecto

Construir una plataforma de servicios TI que pueda:

* desplegarse de forma reproducible;
* operar con alta disponibilidad;
* centralizar identidad y control de acceso;
* monitorear infraestructura y servicios en tiempo real;
* mantener documentación técnica dentro de la propia plataforma;
* aplicar buenas prácticas de seguridad operativa;
* demostrar conocimientos prácticos de Linux, redes, automatización, contenedores, IAM y observabilidad.

---

Proyecto académico orientado a **Infraestructura como Código**, **administración Linux**, **seguridad de servicios**, **contenedores**, **gestión de identidad** y **observabilidad de infraestructura TI**.
