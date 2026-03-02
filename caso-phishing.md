# Caso Práctico: Análisis de Phishing y Exfiltración de Credenciales

## 📋 Descripción del Escenario
Se recibió un reporte en el buzón de abusos sobre un correo sospechoso dirigido al departamento de Finanzas. El mensaje solicitaba una "verificación de cuenta urgente" debido a una supuesta actividad inusual, redirigiendo al usuario a un sitio web externo.

## 🔍 Fase de Análisis Técnico

### 1. Análisis del Correo (Email Header & Body)
Al inspeccionar el código fuente del correo, detecté una técnica de **Link Masking**:
* **Texto visible:** `https://globalbank.com/seguridad`
* **Enlace real (HTML):** `<a href="http://verificar-acceso-globalbank.net/login.php">`

### 2. Auditoría de Logs de Proxy
Investigué la actividad del usuario en el SIEM filtrando por su dirección IP. Se encontraron los siguientes eventos en los logs del **Web Proxy**:

```text
2023-10-27 10:05:10 | 10.0.0.45 | [http://verificar-acceso-globalbank.net](http://verificar-acceso-globalbank.net) | GET | 200
2023-10-27 10:05:45 | 10.0.0.45 | [http://verificar-acceso-globalbank.net/login.php](http://verificar-acceso-globalbank.net/login.php) | POST | 302
Interpretación:

El método GET indica que el usuario cargó la página falsa.

El método POST con código de estado 302 (redirección) confirma que el usuario ingresó datos y estos fueron enviados al servidor del atacante.

🛡️ Respuesta al Incidente y Remediación
Ante la confirmación de la exfiltración de credenciales, se activó el protocolo de contención:

Aislamiento del Host: Se desconectó la estación de trabajo de la red para evitar movimientos laterales de posible malware.

Reseteo de Credenciales: Se forzó el cambio de contraseña en el Active Directory.

Revocación de Sesiones: Se invalidaron todos los tokens de inicio de sesión activos para expulsar al atacante de cualquier aplicación abierta.

Bloqueo de URL: Se añadió el dominio malicioso a la lista de bloqueo del Proxy corporativo.

🛠️ Herramientas Utilizadas
SIEM (Splunk/Sentinel): Para la correlación de logs de red.

Análisis de Código: Inspección de etiquetas HTML del correo.

Herramientas de Identidad: Gestión de usuarios para remediación de cuentas.
