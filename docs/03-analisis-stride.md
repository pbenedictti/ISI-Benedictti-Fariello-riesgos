# Análisis STRIDE

El presente documento tiene como finalidad identificar, analizar y clasificar las amenazas de seguridad que podrían afectar al sistema de Historia Clínica Electrónica (HCE) del hospital, aplicando la metodología STRIDE como enfoque de threat modeling.

El análisis se centra en la protección de datos médicos y en los riesgos asociados a los principales componentes del sistema, considerando la naturaleza altamente sensible de la información clínica procesada. Los datos manejados en el sistema constituyen información médica protegida por lo que su tratamiento exige preservar la confidencialidad, integridad y disponibilidad de la información.

## Alcance del análisis STRIDE

El alcance del análisis STRIDE desarrollado en este documento comprende el flujo de comunicación que se inicia con el usuario paciente y continúa hasta el Backend Hospital, incluyendo los componentes asociados que intervienen en el procesamient de la información requerida para el tratamiento del paciente. Sobre este flujo se aplicará la metodología STRIDE para identificar amenazas que puedan comprometer la seguridad de los datos médicos protegidos.

## Análisis por componentes.

## Componente Usuario Paciente.

**Tipo:** Agente externo

Actor externo que inicia la interacción con el Frontend React (Portal del paciente) desde un dispositivo propio. El hospital no tiene control directo sobre la seguridad del dispositivo utilizado.

|  |  | Riesgo | Amenaza | Descripción del ataque | Mitigaciones técnicas |
|---|---|---|---|---|---|
| S | Spoofing | Crítico | Robo de credenciales / sesión | Phishing dirigido a pacientes para capturar usuario y contraseña del portal. El atacante accede a la historia clínica completa haciéndose pasar por el paciente. | <ul><li>MFA obligatorio</li><li>Detección de login anómalo</li><li>Notificación por email de cada nuevo login</li><li>Cookies HttpOnly+Secure+SameSite</li><li>JWT no en localStorage</li></ul> |
| T | Tampering | Alto | Manipulación de requests (IDOR) | El paciente o un atacante con acceso al navegador modifica parámetros de request para acceder a datos de otros pacientes (Insecure Direct Object Reference). | <ul><li>Validación server-side de todos los parámetros</li><li>Autorización basada en identidad del token JWT</li></ul> |
| R | Repudiation | Medio | Negación de acciones realizadas | El paciente niega haber solicitado un turno, resultado o receta. Sin trazabilidad adecuada es imposible demostrarlo. | <ul><li>Logs de auditoría firmados por acción de usuario</li><li>Timestamps de todas las operaciones en BD</li><li>Confirmación doble para acciones críticas</li></ul> |
| I | Information Disclosure | Crítico | Exposición de HCE por sesión no cerrada | El paciente deja la sesión abierta en dispositivo compartido. Tercero accede a datos médicos sensibles. | <ul><li>Session timeout automático 15 min de inactividad</li><li>Invalidación de sesión en logout en servidor</li><li>Panel de sesiones activas</li></ul> |
| D | Denial of Service | Medio | Lockout forzado de cuenta | Atacante intenta login repetido con el correo del paciente para bloquear su acceso al portal en un momento crítico. | <ul><li>Usar CAPTCHA en intentos fallidos repetidos</li><li>Lockout progresivo con delay exponencial</li><li>Notificar al paciente del lockout con opción de desbloqueo</li></ul> |

## Componente Frontend React.

**Tipo:** Portal paciente web

Interfaz web del Portal del Paciente que se ejecuta en el navegador del usuario y permite interactuar con el Backend del hospital. Al operar en un entorno no controlado directamente por el hospital, queda expuesta a amenazas propias del lado cliente.

|  |  | Riesgo | Amenaza | Descripción del ataque | Mitigaciones técnicas |
|---|---|---|---|---|---|
| S | Spoofing | Alto | Secuestro de sesión mediante XSS | Un script malicioso inyectado en la aplicación roba o reutiliza el token de sesión cuando se almacena de forma insegura en el navegador. | <ul><li>Cookies de sesión HttpOnly + Secure + SameSite</li><li>CSP estricta con allow list mínima</li><li>No almacenar JWT en localStorage</li><li>Sanitización con DOMPurify en contenido dinámico</li><li>Revisión de sinks peligrosos del DOM</li></ul> |
| T | Tampering | Alto | Compromiso de dependencias y manipulación del DOM | Una librería de terceros comprometida modifica el comportamiento de la SPA, altera formularios o redirige datos clínicos hacia un endpoint no autorizado. | <ul><li>SRI en scripts externos</li><li>Auditoría continua de dependencias con npm audit</li><li>Versionado fijo y política formal de actualización</li><li>Revisión de paquetes nuevos antes de incorporarlos. Notas release</li><li>Bloqueo de dominios no autorizados en CSP</li></ul> |
| R | Repudiation | Medio | Acciones críticas sin auditoría server-side | Acciones relevantes ejecutadas desde el frontend no generan evidencia en el backend, por lo que no queda registro verificable para auditoría. | <ul><li>Toda acción significativa debe disparar un evento de auditoría en backend</li><li>No persistir estado crítico solo en el cliente</li><li>Correlación de eventos frontend-backend en SIEM</li><li>Identificador de request para trazabilidad punta a punta</li></ul> |
| I | Information Disclosure | Crítica | Exposición de datos sensibles en frontend u over-fetching | La aplicación expone datos de pacientes en el bundle, comentarios HTML, source maps o respuestas de API con más campos de los necesarios. | <ul><li>FHIR con scopes y campos mínimos necesarios</li><li>No incluir datos sensibles en builds de producción</li><li>Deshabilitar source maps públicos en producción</li><li>Validar contratos de API para evitar over-fetching</li><li>Revisar respuestas con pruebas de privacidad</li></ul> |
| D | Denial of Service | Medio | Clickjacking contra formularios del portal | El portal se embebe en un iframe invisible dentro de un sitio malicioso y el paciente ejecuta acciones sin advertirlo. | <ul><li>X-Frame-Options: DENY</li><li>CSP frame-ancestors 'none'</li><li>Tokens CSRF en formularios y acciones sensibles</li><li>Confirmación explícita para operaciones críticas</li></ul> |
| E | Elevation of Privilege | Alto | XSS almacenado con privilegios de profesional | Un payload XSS guardado en un campo visible para médicos o administradores se ejecuta luego con los permisos de ese perfil privilegiado. | <ul><li>Sanitización de output con DOMPurify</li><li>CSP con nonce por request</li><li>Separación estricta de sesiones admin, profesional y paciente</li><li>Validación y escaping contextual en todos los campos renderizados</li></ul> |

## Componente Backend Hospital

**Tipo:** Servicio / API hospitalaria.

Servicio central del sistema que recibe las solicitudes provenientes del Portal del Paciente, valida la identidad del usuario, aplica las reglas de negocio y coordina el acceso a la información clínica. Este componente actúa como punto de control principal entre el Frontend React, la Base de Datos MySQL, los Servicios Externos / Laboratorio externo.

|  |  | Riesgo | Amenaza | Descripción del ataque | Mitigaciones técnicas |
|---|---|---|---|---|---|
| S | Spoofing | Crítico | Suplantación del laboratorio externo | Un atacante se hace pasar por el laboratorio y envía resultados falsos. Sin autenticación fuerte entre sistemas, el backend podría procesarlos como legítimos. | <ul><li>mTLS con certificado de cliente del laboratorio</li><li>Validación del CN/SAN del certificado en backend</li><li>Firma digital JWS RS256 del payload</li><li>Whitelist de IPs del laboratorio</li><li>API Key rotatoria con espiración corta</li></ul> |
| T | Tampering | Crítico | Inyección SQL contra MySQL | Entradas del usuario o del laboratorio llegan al backend sin control y permiten construir consultas SQL maliciosas, con riesgo de extracción, modificación o borrado de datos. | <ul><li>ORM o queries parametrizadas, sin concatenación de SQL</li><li>Validación de esquema</li><li>WAF con OWASP CRS en modo bloqueo</li><li>Usuario de BD con mínimo privilegio</li><li>Procedimientos almacenados para operaciones críticas</li></ul> |
| T | Tampering | Crítico | Manipulación de resultados clínicos en tránsito | Un atacante MitM altera el payload de resultados del laboratorio antes de que llegue al backend, pudiendo cambiar valores clínicos relevantes. | <ul><li>TLS 1.3 exclusivo con validación estricta de certificados</li><li>Firma digital JWS RS256 del payload por el laboratorio</li><li>Verificación de firma, timestamp y nonce en middleware</li></ul> |
| R | Repudiation | Alto | Operaciones administrativas sin trazabilidad | Un administrador realiza consultas masivas, exportaciones o cambios sensibles sin que quede evidencia suficiente de la acción y su justificación. | <ul><li>Log inmutable de operaciones administrativas con identidad del operador</li><li>Alertas SIEM por consultas o exportaciones masivas</li><li>Segregación de funciones entre roles administrativos y operativos</li><li>Justificación obligatoria para acciones sensibles</li></ul> |
| I | Information Disclosure | Crítico | Exposición de datos sensibles en logs | El backend registra payloads completos, tokens, contraseñas o datos clínicos en texto claro dentro de archivos de log o herramientas de monitoreo. | <ul><li>Política de no-log Enmascaramiento automático en el logger</li><li>Cifrado de logs en disco y en tránsito</li><li>WORM storage para logs críticos</li><li>Revisión periódica de patrones de logging</li></ul> |
| D | Denial of Service | Alto | Agotamiento de recursos por solicitudes masivas | Un laboratorio comprometido o un atacante envía alto volumen de requests y agota conexiones, CPU o capacidad de la base de datos. | <ul><li>Rate limiting por IP, token y cliente en API Gateway</li><li>Circuit breaker hacia servicios dependientes.Tolerancia a fallas</li><li>Límites de concurrencia por endpoint</li><li>Health checks y auto-scaling con umbrales controlados</li></ul> |
| E | Elevation of Privilege | Crítico | SSRF hacia red interna o metadata cloud | Un parámetro URL controlado por el atacante fuerza al backend a consultar servicios internos o endpoints de metadata cloud para obtener credenciales o datos no expuestos. | <ul><li>Eliminar parámetros URL controlados por usuario cuando sea posible</li><li>Allowlist estricta de dominios y bloqueo de IPs privadas</li><li>Firewall de egreso: backend solo alcanza destinos autorizados</li><li>IMDSv2 obligatorio en instancias cloud</li><li>Validación DNS y bloqueo de redirecciones</li></ul> |

## Resumen por componente.

| Componente | Tipo | Total amenazas | Críticas | Altas | Medias | Riesgo global |
|---|---|---:|---:|---:|---:|---|
| Usuario paciente | Agente externo | 5 | 2 | 1 | 2 | Alto |
| Frontend React | Interfaz web SPA | 6 | 1 | 3 | 2 | Alto |
| Backend Hospital | Servicio central | 7 | 4 | 2 | 1 | Crítico |



## Referencias generales:

1- STRIDE Threat Modeling Example for Better Understanding and Learning: https://threat-modeling.com/stride-threat-modeling-example/ 

2- Threat Modeling Process | OWASP Foundation: https://owasp.org/www-community/Threat_Modeling_Process
