# Riesgos residuales

El presente documento tiene como objetivo identificar el riesgo residual resultante luego de aplicar las acciones definidas en el Plan de Mitigación.

El riesgo residual corresponde al nivel de riesgo que permanece una vez implementadas las contramedidas previstas para cada amenaza. Su análisis permite determinar si el riesgo queda reducido a un nivel aceptable, si requiere seguimiento o si debe mantenerse bajo control hasta contar con evidencia técnica de mitigación.

# **Alcance**

El análisis de riesgos residuales se basa exclusivamente en las amenazas incluidas en el plan de mitigación definido para el flujo evaluado. El análisis considera únicamente el riesgo remanente esperado luego de aplicar las acciones establecidas.

# **Criterios utilizados**

Para este documento se considera el siguiente criterio de evaluación para el riesgo:

| Riesgo  | Criterio |
| :---: | ----- |
| **Bajo** | La mitigación reduce significativamente la probabilidad de explotación y el impacto esperado. Requiere monitoreo normal. |
| **Medio** | La mitigación reduce el riesgo, pero permanece exposición por configuración, error humano, nuevas variantes de ataque o dependencia de controles operativos. Requiere seguimiento. |
| **Alto** | El riesgo continúa siendo relevante incluso con mitigaciones aplicadas. Requiere revisión prioritaria. |
| **Crítico** | El riesgo no queda reducido a un nivel aceptable. No debería aceptarse sin acciones adicionales. |

Para evaluar el impacto del riesgo residual se utiliza la misma escala de Daño aplicada en la matriz DREAD. Esto permite evaluar el riesgo inicial y el riesgo residual bajo un mismo criterio, asegurando consistencia en la medición y facilitando la comparación de los resultados. 

| Daño (DREAD) | Nivel | Descripción |
| :---: | :---: | ----- |
| 10 | Crítico | Destrucción completa del sistema |
| 9 | Muy alto | Toda la base de datos comprometida |
| 7 | Alto | Pérdida significativa de datos |
| 6 | Medio | Pérdida parcial de funcionalidad |
| 4 | Bajo | Pérdida mínima |
| 3 | Muy bajo | Sin impacto técnico significativo |
| 0 | Ninguno | Sin impacto |

# **Matriz de riesgos residuales**

La siguiente matriz presenta el nivel de riesgo que permanece luego de aplicar las mitigaciones definidas. Su objetivo es visualizar qué amenazas quedan bajo control, cuáles requieren seguimiento y cuáles deben mantenerse monitoreadas por su exposición residual. 

| ID Riesgo | Amenaza | Riesgo residual | Impacto | Justificación |
| ----- | ----- | ----- | ----- | ----- |
| R01 | Robo de credenciales y sesión | Medio | Medio | Se reduce el impacto del robo de credenciales, pero persiste el riesgo debido a phishing, ingeniería social o dispositivos comprometidos. |
| R02 | Manipulación de resultados clínicos en tránsito | Bajo | Medio | La combinación de controles recomendados, reduce significativamente la posibilidad de modificación no autorizada del payload. |
| R03 | Exposición de datos sensibles en logs | Medio | Medio | Si bien se reduce la exposición, el riesgo no desaparece completamente, ya que pueden haber errores de logging o errores de configuración en las herramientas de monitoreo. |
| R04 | SSRF hacia red interna o metadata cloud | Medio | Muy Alto | Se reduce la superficie de ataque, pero puede persistir riesgo por errores de validación, redirecciones, resolución de DNS o errores de configuración en excepciones. |
| R05 | Operaciones administrativas sin trazabilidad | Bajo | Bajo | Las acciones permiten registrar y controlar las operaciones sensibles, lo que reduce significativamente el riesgo de acciones sin evidencia. |
| R06 | Agotamiento de recursos por solicitudes masivas | Medio | Crítico | Los controles reducen el impacto operativo, pero pueden persistir escenarios de carga elevada o configuraciones insuficientes de umbrales. |
| R07 | Exposición de HCE por sesión activa | Bajo | Bajo | Se reduce la posibilidad de acceso indebido por sesiones abiertas o dispositivos compartidos, pero debe mantenerse monitoreo de sesiones activas. |
| R08 | Manipulación de solicitudes por IDOR | Medio | Alto | Los errores de autorización en general dependen de reglas de negocio y casos funcionales. Se requieren pruebas y revisión continua. |
| R09 | Exposición de datos sensibles en frontend | Medio | Muy bajo | Disminuye la exposición pero se deben tener en cuenta los controles en futuros cambios de APIs. Además, se puede mantener el riesgo por respuestas excesivas o errores en el proceso de build. |
| R10 | Secuestro de sesión mediante XSS | Medio | Alto | Aunque se reduce el robo de sesión, un XSS podría afectar la interacción del usuario, ejecutar acciones en una sesión activa o exponer información visible en pantalla. |
| R11 | Compromiso de dependencias y manipulación del DOM | Medio | Alto | Continúa habiendo exposición frente a vulnerabilidades nuevas, paquetes comprometidos o dependencias que no cuenten aún con detección automática. |
| R12 | XSS almacenado con privilegios de profesional | Medio | Alto | El riesgo disminuye, pero se deben tener en cuenta estos controles para nuevos campos persistentes. Además, cuidar errores de renderizado y fallas en el escaping contextual. |
| R13 | Suplantación del laboratorio externo | Bajo | Bajo | Los controles de autenticación fuerte entre sistemas reducen significativamente la posibilidad. |
| R14 | Inyección SQL contra MySQL | Bajo | Alto | Las acciones reducen fuertemente la probabilidad de explotación. Sin embargo, permanece riesgo residual por nuevos endpoints, errores de implementación, consultas no parametrizadas o reglas WAF incompletas. |

# **Resumen de riesgos residuales**

Luego de aplicar las acciones definidas en el plan de mitigación, no se mantienen riesgos residuales estimados como críticos o altos. Sin embargo, la mayoría de las amenazas conservan un riesgo residual medio, principalmente por la posibilidad de errores de configuración, cambios futuros en el sistema, nuevas variantes de ataque o dependencia de controles operativos.

Las amenazas con riesgo residual bajo corresponden a escenarios donde las mitigaciones definidas incorporan controles fuertes y específicos, como autenticación entre sistemas, cifrado, trazabilidad o gestión de sesiones.

| Riesgo | Cantidad de amenazas | Amenazas |
| :---: | :---: | ----- |
| Bajo | 5 | R02, R05, R07, R13, R14 |
| Medio | 9 | R01, R03, R04, R06, R08, R09, R10, R11, R12 |
| Alto | 0 | No se identifican riesgos residuales altos si las mitigaciones se implementan correctamente. |
| Crítico | 0 | No se identifican riesgos residuales críticos si las mitigaciones se implementan correctamente. |

