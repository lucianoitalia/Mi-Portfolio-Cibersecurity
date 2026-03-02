# Caso Práctico: Investigación de Intrusión por Fuerza Bruta (SSH)

## 📋 Descripción del Escenario
Como Analista SOC, se recibió una alerta automática del sistema de monitoreo indicando múltiples fallos de autenticación en un servidor Linux de producción que aloja bases de datos críticas.

## 🔍 Fase de Análisis (Investigación de Logs)
Al acceder al archivo de auditoría `/var/log/auth.log`, identifiqué el siguiente patrón de ataque:

```text
Oct 26 22:10:01 server-db sshd[1234]: Failed password for root from 185.220.101.34 port 54322 ssh2
Oct 26 22:10:02 server-db sshd[1234]: Failed password for root from 185.220.101.34 port 54325 ssh2
Oct 26 22:10:04 server-db sshd[1234]: Failed password for admin from 185.220.101.34 port 54328 ssh2
... [50 intentos adicionales en 60 segundos] ...
Hallazgos clave:
Tipo de Ataque: Fuerza Bruta (Diccionario) dirigido a usuarios comunes (root, admin).

Indicador de Compromiso (IOC): Dirección IP atacante 185.220.101.34.

Severidad: Alta, debido a la persistencia y velocidad de los intentos.

🛡️ Respuesta al Incidente y Remediación
Para contener la amenaza, ejecuté las siguientes acciones:

Bloqueo de IP: Se añadió la IP atacante a la lista negra del Firewall perimetral (Baneo permanente).

Verificación de Acceso: Confirmé que no hubo ningún log de Accepted password posterior a los intentos fallidos, descartando una intrusión exitosa.

Hardening (Fortalecimiento): Recomendé al equipo de infraestructura la implementación de Fail2Ban y la desactivación del login de root por SSH.

🛠️ Herramientas Utilizadas
Linux CLI: (Grep, Tail) para el filtrado de logs.

IP Intelligence: Verificación de reputación de la IP en bases de datos de amenazas.
