# Informe de configuración de DMZ con Cisco Packet Tracer

### 1. Objetivo del laboratorio
Configurar una Zona Desmilitarizada (DMZ) utilizando un router Cisco ISR para alojar un servidor web, permitiendo acceso controlado desde la red externa y la red interna, mientras se aísla la red interna de la DMZ.

---

### 2. Topología implementada

* **Tipo:** Estrella
* **Cantidad de redes:** 3 (Internal Network, DMZ, External Network).
* **Dispositivos usados:** 1 Router Cisco ISR 2911 (`Router_FW`), 3 Switches Cisco 2960, 2 PCs (`PC_Internal`, `PC_External`) y 1 Servidor Web (`Server-PT Web_DMZ`).
* **Descripción de zonas:**
    * **LAN (Internal Network):** Red privada y segura para los usuarios internos.
    * **DMZ:** Zona aislada que aloja los servicios públicos, exponiendo de forma controlada el servidor web.
    * **Externa:** Simulación del internet público desde donde los usuarios acceden a los servicios de la DMZ.

---

### 3. Plan de direccionamiento IP

| Dispositivo | IP | Máscara | Gateway |
| :--- | :--- | :--- | :--- |
| **PC_Internal** | 192.168.1.10 | 255.255.255.0 | 192.168.1.1 |
| **Server_DMZ** | 192.168.2.10 | 255.255.255.0 | 192.168.2.1 |
| **PC_External** | 192.168.3.10 | 255.255.255.0 | 192.168.3.1 |
| **Router_FW Gi0/0 (LAN)** | 192.168.1.1 | 255.255.255.0 | N/A |
| **Router_FW Gi0/1 (DMZ)** | 192.168.2.1 | 255.255.255.0 | N/A |
| **Router_FW Gi0/2 (Ext)** | 192.168.3.1 | 255.255.255.0 | N/A |

---

### 4. Configuración aplicada (resumen)

* **Configuración de NAT Estático:** Se mapeó la dirección IP privada del servidor web en la DMZ a una dirección IP pública en la interfaz externa del router.
    ```bash
    ip nat inside source static 192.168.2.10 192.168.3.1
    ```
* **Listas de Control de Acceso (ACLs):** Se aplicaron reglas extendidas para restringir el tráfico.
    * **ACL 101 (Entrada Gi0/2):** Permite tráfico HTTP y HTTPS desde el exterior, bloqueando implícitamente ICMP (Ping).
        ```bash
        ip access-list extended 101
        permit tcp any host 192.168.3.1 eq 80
        permit tcp any host 192.168.3.1 eq 443
        ```
    * **ACL 100 (Entrada Gi0/1):** Bloquea el tráfico originado en la DMZ hacia la red interna, pero permite el tráfico de retorno de las sesiones TCP y las respuestas ICMP previamente establecidas por la LAN.
        ```bash
        ip access-list extended 100
        permit tcp any 192.168.1.0 0.0.0.255 established
        permit icmp any 192.168.1.0 0.0.0.255 echo-reply
        deny ip 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255
        permit ip any any
        ```

---

### 5. Verificaciones realizadas

* **Desde PC_External (Web Browser):** Se accedió al servidor web DMZ mediante la IP pública `192.168.3.1`. **Resultado:** La página web cargó exitosamente.
* **Desde PC_External (Command Prompt):** Se ejecutó `ping 192.168.3.1`. **Resultado:** *Request timed out*, confirmando que la ACL externa bloquea el tráfico ICMP.
* **Desde PC_Internal (Web Browser):** Se accedió al servidor web DMZ mediante la IP privada `192.168.2.10`. **Resultado:** La página web cargó exitosamente.
* **Desde Server-PT Web_DMZ (Command Prompt):** Se ejecutó `ping 192.168.1.10` hacia la LAN. **Resultado:** *Request timed out*, validando el aislamiento de seguridad de la red interna.

---

### 6. Conclusiones y recomendaciones

La implementación de políticas de seguridad en red requiere un enfoque riguroso de verificación. Ejecutar casos de prueba manuales precisos para validar no solo el tráfico permitido, sino también asegurar que el tráfico no autorizado sea efectivamente denegado, es la única forma de garantizar una postura de seguridad sólida. Durante esta práctica, se evidenció que el orden y la dirección de aplicación de las ACLs (entrada vs. salida) son críticos; un pequeño error de lógica en las reglas puede resultar en falsos positivos de seguridad o bloquear servicios legítimos. Se recomienda siempre validar la conectividad básica (Capa 3) antes de implementar el filtrado de paquetes.

---

### 7. Capturas de evidencia
* **[Diagrama de Red](https://drive.google.com/file/d/1eyCTh4mAY_gfE0H3KFUECuiIDGbACgHP/view?usp=drive_link)**
* **[Navegador en PC Externa](https://drive.google.com/file/d/1lijQCCGz9zilYxUB2njqboxCx7LcDoxC/view?usp=drive_link)**
* **[Ping en PC Externa](https://drive.google.com/file/d/1FK6QHck8QT9A5bQjJbko7Q_nEAa2LFJB/view?usp=drive_link)**
* **[Ping en PC Interna](https://drive.google.com/file/d/1TefOHTcb1V5HUGZ7K8yW6BuVElGwOtqS/view?usp=drive_link)**
* **[Ping de Servidor Web](https://drive.google.com/file/d/1qMH_H8X01CopMUt1mGpVDfreo2Q-4UpI/view?usp=drive_link)**