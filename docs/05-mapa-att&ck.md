# 05-mapa-ATT\&CK

Este documento tiene como objetivo desarrollar el análisis de amenazas en base al framework ATT\&CK de Mitre.

Para llevar a cabo el mismo, se investigaron las actividades realizadas por diferentes grupos dedicados al ciberataque.

Uno de ellos, es el grupo Orangeworm, que ha tenido como objetivo organizaciones del sector de salud en Estados Unidos, Europa y Asia desde el año 2015 aproximadamente. Su principal objetivo es el espionaje corporativo.(1)(2)

Otro grupo que se interesa principalmente en el robo de información sensible, y estuvo relacionado con un ataque contra la organización de salud más grande de Singapur.(3)(4)

## **Escenario hipotético planteado:**

**Actor:** Whitefly 

**Objetivo:** Obtener datos sensibles como historias clínicas y resultados de laboratorio.

**Punto de entrada:** Aplicación Web y API REST

**Flujo del ataque:**  
El atacante escanea el portal para pacientes y la API REST/FHIR expuesta para identificar versiones de React, endpoints de autenticación o la estructura de las peticiones hacia el Backend.

Intercepta y manipula el tráfico entre la Aplicación React y la API REST/FHIR, cruzando la "Frontera de Confianza 1". Busca explotar vulnerabilidades en el portal del paciente (ej. fallas de autenticación) o uso de credenciales comprometidas (T1078) para ingresar al Frontend como un usuario legítimo.

Luego, itnenta explotar vulnerabilidades de inyección en los *endpoints* o fallos en la validación de tokens JWT. También podría intentar exportar una vulnerabilidad de Cross-Site Scripting (XSS) en el Frontend de React para ejecutar scripts en los navegadores de otros usuarios, o enviar payloads hacia la API.

Al lograr la ejecución de código en la API, el intruso accede al entorno del servidor de aplicaciones.   
Una vez interactuando con el Backend, el atacante mapea las conexiones internas que realiza el servidor, descubriendo la Base de Datos MySQL y las rutas de integración con el Laboratorio externo. También podría buscar en el código fuente, archivos de configuración o variables de entorno las cadenas de conexión y contraseñas necesarias para acceder a la base de datos. En cualquiera de estos casos,  estaría cruzando hacia la "Frontera de Confianza 2".

Utilizando las credenciales de base de datos comprometidas en el paso anterior, el atacante se conecta a la instancia MySQL. Ejecuta scripts automatizados para realizar un volcado masivo de las tablas que contienen registros de pacientes, notas de evolución y prescripciones, enviando estos datos a un servidor externo. Esta extracción se realizaría evadiendo los controles perimetrales.

También podría interceptar los resultados de laboratorio en tránsito, pivotando desde el backend hacia el servicio del laboratorio externo, aprovechándose de la conexión de confianza.

## **Técnicas identificadas**

| Reconnaissance  |  |
| :---- | :---- |
| **ID** | T1595 |
| **Técnica**  | Active Scanning  |
| **Activo Afectado**  | Aplicación Web (React) / API REST  |
| **Mitigación**  | <ul><li>Implementar un WAF (Web Application Firewall) para bloquear patrones de escaneo de vulnerabilidades.</li><li>Configurar Rate Limiting (limitación de tasa) estricto por IP en la Frontera de Confianza 1 para frenar el escaneo automatizado.</li></ul>  |

| Initial Access  |  |
| :---- | :---- |
| **ID** | T1190  |
| **Técnica**  | Exploit Public-Facing Application  |
| **Activo Afectado**  | Aplicación Web (React) / Credenciales  |
| **Mitigación**  | <ul><li>Validación estricta de entradas y parametrización en la API.</li><li>Mantener dependencias actualizadas.</li><li>Ejecutar análisis DAST/SAST en el ciclo de desarrollo para detectar vulnerabilidades en el código antes de producción.</li></ul>  |

| Execution |  |
| :---- | :---- |
| **ID** | T1059 |
| **Técnica**  | Command and Scripting Interpreter |
| **Activo Afectado**  | Aplicación Web (React) |
| **Mitigación**  | <ul><li>Aplicar el Principio de Menor Privilegio a la cuenta de servicio o contenedor (Docker) que ejecuta la aplicación web/backend.</li><li> Implementar políticas de seguridad de contenido (CSP) estrictas en React.</li></ul> |

| Privilege Escalation |  |
| :---- | :---- |
| **ID** | T1134 |
| **Técnica**  | Access Token Manipulation |
| **Activo Afectado**  | API REST \+ FHIR |
| **Mitigación**  | <ul><li>Validación criptográfica de los tokens JWT (firma, expiración, emisor).</li><li>Usar tokens de corta duración, combinados con Refresh Tokens, y mantener una lista de revocación en el backend.</li></ul> |

| Defense Evasion |  |
| :---- | :---- |
| **ID** | T1070 |
| **Técnica**  | Indicator Removal |
| **Activo Afectado**  | Logs de auditoría. |
| **Mitigación**  | <ul><li>Centralización de logs enviando los eventos a un SIEM o servidor remoto en tiempo real (modo append-only).</li><li>Restringir los permisos para que la cuenta del servidor de aplicaciones no pueda modificar o borrar los archivos de registro locales.</li></ul> |

| Lateral Movement |  |
| :---- | :---- |
| **ID** | T1210 |
| **Técnica**  | Exploitation of Remote Services |
| **Activo Afectado**  | Integración con laboratorios |
| **Mitigación**  | <ul><li>Aplicar Autenticación Mutua (mTLS) y Listas Blancas de IP estrictas en el firewall de la Frontera de Confianza 4\.</li><li> Deshabilitar protocolos de administración remota no esenciales (como RDP o SSH) en esas interfaces.</li></ul> |

| Collection |  |
| :---- | :---- |
| **ID** | T1119 |
| **Técnica**  | Automated Collection |
| **Activo Afectado**  | Base de Datos MySQL |
| **Mitigación**  | <ul><li>Monitoreo de Actividad de Base de Datos para alertar sobre consultas anómalas.</li><li>Aplicar privilegios mínimos a la conexión JDBC: la API sólo debe poder consultar/modificar las tablas necesarias.</li></ul> |

| Exfiltration |  |
| :---- | :---- |
| **ID** | T1567 |
| **Técnica**  | Exfiltration Over Web Service |
| **Activo Afectado**  | Datos de Registro |
| **Mitigación**  | <ul><li>Filtrado de tráfico de salida (Egress filtering) en los firewalls.</li><li>El servidor de base de datos y el backend solo deben poder comunicarse con destinos explícitamente autorizados, bloqueando conexiones hacia servicios de almacenamiento en la nube externos no corporativos.</li></ul> |

| Impact |  |  |
| :---- | :---- | :---- |
| **ID** | T1485 | T1565 |
| **Técnica**  | Data Destruction | Data Manipulation |
| **Activo Afectado**  | Prescripciones / Resultados de Laboratorio |  |
| **Mitigación**  | <ul><li>Políticas de Backups inmutables y cifrados (como se ve en la "Red Backups" de tu arquitectura) alojados fuera de línea o en zonas aisladas.</li><li> Probar la restauración periódicamente.</li></ul> | Implementar firmas digitales o hashing en los registros de la base de datos para detectar alteraciones. </li><li> Controles de acceso basados en atributos (ABAC) para evitar modificaciones no autorizadas de las historias clínicas.</li></ul> |

En base al análisis realizado, se desprende que el componente más crítico en la arquitectura del sistema, y al que más controles se deben realizar, es el Backend del hospital.

## **Referencias**

[Orangeworm, Group G0071 | MITRE ATT\&CK®](https://attack.mitre.org/groups/G0071/) (1)

[New Orangeworm attack group targets the healthcare sector in the U.S., Europe, and Asia | SECURITY.COM](https://www.security.com/threat-intelligence/orangeworm-targets-healthcare-us-europe-asia) (2)

[Whitefly, Group G0107 | MITRE ATT\&CK®](https://attack.mitre.org/groups/G0107/) (3)

[Whitefly: Espionage Group has Singapore in Its Sights | SECURITY.COM](https://www.security.com/threat-intelligence/whitefly-espionage-singapore) (4)