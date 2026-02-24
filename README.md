# 🛡️ Construcción y Seguridad de una Red con DMZ

Este repositorio contiene la resolución del laboratorio práctico de configuración de una Zona Desmilitarizada (DMZ) en Cisco Packet Tracer, desarrollado como parte de mi formación en el bootcamp de Ciberseguridad de 4Geeks Academy.

## 🎯 Objetivo del Proyecto
El objetivo principal de este laboratorio es implementar competencias fundamentales de ciberseguridad defensiva. Se configuró una red segmentada para:
- Aislar servicios críticos (DMZ) de la red interna (LAN).
- Exponer de forma controlada un servidor web hacia el exterior (Internet).
- Implementar NAT estático para el enmascaramiento de IPs.
- Controlar el flujo de tráfico mediante Listas de Control de Acceso (ACLs) configuradas en un router Cisco ISR.

## 🕸️ Arquitectura de la Red
El diseño lógico y físico de este proyecto se basa en una **Topología en Estrella Extendida**. 
- **Nodo Central:** Un router Cisco ISR 2911 (`Router_FW`) que actúa como el núcleo de la red, obligando a que todo el tráfico inter-zonas pase por él para su inspección.
- **Nodos Secundarios:** Tres switches Cisco 2960 que distribuyen la conexión hacia los dispositivos finales en cada una de las tres zonas aisladas (Internal Network, DMZ y External Network).

## 📂 Contenido del Repositorio
- `DMZ_PROJECT_FINAL.pka`: Archivo final de Packet Tracer con la topología y las configuraciones de red, NAT y ACLs aplicadas.
- `informe/Informe_DMZ_Laboratorio.md`: Documentación técnica detallada con el plan de direccionamiento IP, comandos ejecutados y resultados de las pruebas de seguridad.
- `evidencias/`: Directorio que contiene las capturas de pantalla que validan el funcionamiento del servidor web y el bloqueo de tráfico ICMP (ping) no autorizado.

## 🛠️ Tecnologías y Herramientas Utilizadas
- Cisco Packet Tracer
- Cisco IOS (CLI)
- Configuración de IPv4, NAT estático y ACLs (Extended Access Lists)
- Protocolos: TCP (HTTP/HTTPS), ICMP

## 🚀 Cómo probar este laboratorio
1. Clona este repositorio.
2. Abre el archivo `.pka` con Cisco Packet Tracer.
3. Desde la `PC_External`, abre el navegador y navega a `http://192.168.3.1` para verificar el acceso web mediante NAT.
4. Desde la `Server-PT Web_DMZ`, intenta hacer un ping a la IP de la LAN `192.168.1.10` (debe resultar en *Request timed out* debido a las reglas del firewall).