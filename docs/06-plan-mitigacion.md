# Plan de mitigación

El presente plan de mitigación se elabora a partir de las amenazas identificadas y evaluadas mediante DREAD, con el objetivo de priorizar acciones de seguridad según el nivel de riesgo obtenido. La priorización permite enfocar los esfuerzos en aquellas amenazas con mayor impacto potencial sobre la confidencialidad, integridad y disponibilidad de la Historia Clínica Electrónica.

El criterio utilizado considera el puntaje DREAD y la severidad asignada a cada amenaza. Las amenazas críticas requieren tratamiento inmediato, las amenazas altas deben abordarse en el corto plazo y las amenazas medias se incorporan a una planificación controlada de mejora y seguimiento.

# **Alcance de la mitigación**

El alcance de este plan de mitigación se limita al flujo analizado en los documentos 03 \- Análisis STRIDE y 04 \- Priorización DREAD. Las acciones definidas se enfocan exclusivamente en las amenazas identificadas y priorizadas dentro de dicho flujo, considerando los componentes, actores, integraciones y activos evaluados previamente.

# **Criterio de priorización**

La priorización se define en función del puntaje DREAD obtenido para cada amenaza. No todas las amenazas dentro de una misma severidad tienen el mismo orden de atención; por este motivo, el plan se organiza de mayor a menor criticidad.

| Puntaje DREAD | Severidad | Tratamiento |
| ----- | ----- | ----- |
| 9,0 a 10 | Crítico | Mitigación inmediata |
| 7,0 a 8,9 | Alto | Mitigación prioritaria en corto plazo |
| 5,0 a 6,9 | Medio | Mitigación planificada |
| 0 a 4,9 | Bajo | Monitoreo y mejora continua |

# **Matriz de prioridad de amenazas** 

A partir de las amenazas identificadas y priorizadas, se definen contramedidas orientadas a reducir la probabilidad de explotación y el impacto sobre el sistema. Las medidas propuestas consideran controles técnicos, operativos y de seguridad aplicables al flujo analizado.

| Prioridad | DREAD ID | Amenaza | Severidad | Acción de mitigación | Plazo sugerido |
| :---: | :---: | ----- | ----- | ----- | ----- |
| 1 | V9 | Inyección SQL contra MySQL | Crítico | Usar queries parametrizadas, ORM, validación de esquema, WAF y usuario de base con mínimo privilegio. | Inmediato |
| 2 | V14 | Agotamiento de recursos por solicitudes masivas | Crítico | Implementar rate limiting, circuit breaker, límites de concurrencia y monitoreo de consumo. | Inmediato |
| 3 | V11 | Exposición de datos sensibles en logs | Alto | Aplicar política de no-log para PHI, tokens y contraseñas. Incorporar enmascaramiento automático y cifrado de logs. | Corto plazo |
| 4 | V10 | Manipulación de resultados clínicos en tránsito | Alto | Usar TLS 1.3, firma digital del payload, timestamp, nonce y validación de rangos clínicos. | Corto plazo |
| 5 | V12 | SSRF hacia red interna o metadata cloud | Alto | Implementar allowlist estricta de dominios, bloquear IPs privadas, aplicar firewall de egreso y evitar URLs controladas por usuario. | Corto plazo |
| 6 | V4 | Exposición de datos sensibles en frontend | Alto | Aplicar minimización de datos, revisar contratos de API, deshabilitar source maps públicos y evitar datos sensibles en builds. | Corto plazo |
| 7 | V8 | Suplantación del laboratorio externo | Alto | Implementar mTLS, validación de certificado, firma JWS, whitelist de IPs y API Key rotatoria. | Corto plazo |
| 8 | V3 | Manipulación de solicitudes por IDOR | Alto | Validar permisos server-side, aplicar controles de ownership por recurso y pruebas específicas de autorización. | Corto plazo |
| 9 | V6 | Compromiso de dependencias y manipulación del DOM | Medio | Usar versionado fijo, npm audit/Snyk, revisión de paquetes, SRI y bloqueo de dominios no autorizados en CSP. | Mediano plazo |
| 10 | V5 | Secuestro de sesión mediante XSS | Medio | Usar cookies HttpOnly, Secure y SameSite, CSP estricta, sanitización con DOMPurify y evitar JWT en localStorage. | Mediano plazo |
| 11 | V7 | XSS almacenado con privilegios de profesional | Medio | Sanitización de salida, escaping contextual, CSP con nonce y separación de sesiones por perfil. | Mediano plazo |
| 12 | V1 | Robo de credenciales y secuestro de sesión | Medio | Aplicar MFA, detección de login anómalo, notificación de nuevos accesos y gestión de sesiones activas. | Mediano plazo |
| 13 | V13 | Operaciones administrativas sin trazabilidad | Medio | Implementar logs inmutables, alertas SIEM, segregación de funciones y justificación obligatoria. | Mediano plazo |
| 14 | V2 | Exposición de HCE por sesión activa | Medio | Timeout automático, cierre de sesión server-side, aviso previo y panel de sesiones activas. | Mediano plazo |

# **Controles por categoría**

## **Controles Preventivos**

Diseñados para bloquear vulnerabilidades, limitar el acceso y evitar que un incidente de seguridad llegue a materializarse.

* MFA obligatorio  
* Autorización basada en identidad del token JWT  
* Separación estricta de sesiones admin, profesional y paciente  
* Usuario de BD con mínimo privilegio  
* Segregación de funciones entre roles administrativos y operativos  
* Justificación obligatoria para acciones sensibles  
* Cookies HttpOnly \+ Secure \+ SameSite  
* JWT no en localStorage  
* Session timeout automático 15 min de inactividad  
* Invalidación de sesión en logout en servidor  
* API Key rotatoria con expiración corta  
* CSP estricta con allow list mínima / CSP con nonce por request / Bloqueo de dominios no autorizados  
* Sanitización con DOMPurify en contenido dinámico  
* Validación y escaping contextual en todos los campos renderizados  
* Revisión de sinks peligrosos del DOM  
* SRI (Subresource Integrity) en scripts externos  
* Deshabilitar source maps públicos / No incluir datos sensibles en builds de producción  
* Versionado fijo y política formal de actualización / Revisión de paquetes nuevos y Release Notes  
* Eliminar parámetros URL controlados por usuario cuando sea posible  
* Validación server-side de todos los parámetros / Validación de esquema   
* Validar contratos de API para evitar over-fetching / FHIR con scopes y campos mínimos necesarios  
* ORM o queries parametrizadas, sin concatenación de SQL  
* Procedimientos almacenados para operaciones críticas  
* WAF con OWASP CRS en modo bloqueo.  
* Rate limiting por IP, token y cliente en API Gateway / Límites de concurrencia por endpoint  
* TLS 1.3 exclusivo con validación estricta de certificados  
* mTLS con certificado de cliente del laboratorio / Validación del CN/SAN en backend  
* Firma digital JWS RS256 del payload / Verificación de firma, timestamp y nonce en middleware  
* Cifrado de logs en disco y en tránsito  
* Política de no-log y enmascaramiento automático en el logger  
* WORM (Write Once, Read Many) storage para logs críticos  
* Whitelist de IPs del laboratorio / Allowlist estricta de dominios y bloqueo de IPs privadas  
* Firewall de egreso (backend solo alcanza destinos autorizados)  
* IMDSv2 obligatorio en instancias cloud  
* Validación DNS y bloqueo de redirecciones

  ## **Controles Detectivos**

Estos no detienen el ataque, pero proporcionan visibilidad, alertas y pruebas de que un incidente está ocurriendo o ha ocurrido.

* Detección de login anómalo  
* Notificación por email de cada nuevo login  
* Panel de sesiones activas  
* Alertas SIEM por consultas o exportaciones masivas  
* Log inmutable de operaciones administrativas con identidad del operador  
* Revisión periódica de patrones de logging  
* Auditoría continua de dependencias con npm audit  
* Revisar respuestas con pruebas de privacidad

  ## **Controles Correctivos**

Actúan *después* de que se ha detectado una falla, un ataque o una condición adversa, con el objetivo de restaurar el servicio o mitigar el impacto en tiempo real.

* Circuit breaker hacia servicios dependientes (Tolerancia a fallas)  
* Health checks y auto-scaling con umbrales controlados