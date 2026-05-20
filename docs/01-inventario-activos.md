# **Inventario de Activos Críticos \- Sistema HCE**

# **Contexto**

La Historia Clínica Electrónica representa la consolidación técnica de la información asistencial del paciente en plataformas digitales interoperables, donde los datos clínicos, administrativos y diagnósticos se registran, almacenan, consultan y comparten mediante repositorios electrónicos integrados, garantizando trazabilidad, disponibilidad, integridad y seguridad de la información médica. 

**Fundamentos normativos**

La identificación y protección de los activos tecnológicos del sistema HCE requiere alinearse con marcos normativos y buenas prácticas reconocidas en seguridad de la información y salud. En este contexto, se consideran como referencias principales ISO/IEC 27001, ISO 27799, HIPAA y NIST SP 800-66r2, ya que aportan criterios para gestionar riesgos, proteger información clínica sensible y definir controles técnicos, administrativos y físicos.

ISO/IEC 27001 brinda la base para implementar un Sistema de Gestión de Seguridad de la Información, mientras que ISO 27799 adapta estos controles al sector salud, enfocándose en la confidencialidad, integridad y disponibilidad de la información personal de salud. A su vez, HIPAA y NIST SP 800-66r2 refuerzan la necesidad de realizar análisis de riesgos, gestionar vulnerabilidades y aplicar salvaguardas que eviten accesos no autorizados o divulgación indebida de historias clínicas electrónicas.

En conjunto, estos marcos permiten fundamentar la criticidad de los activos del sistema HCE, considerando su impacto sobre la seguridad, la privacidad, la continuidad operativa y la protección de los datos médicos. (1)

# **Activos Identificados**

## **Activos de Información (Los datos PHI puros)**

Estos son los datos generados y procesados por el hospital que constituyen la Información de Salud Protegida, considerada de alta criticidad por su impacto en la privacidad de los pacientes y cumplimiento loa marco normativo (como HIPAA, AGESIC)

| Activo | Tipo | Descripción | Criticidad  | Justificación  |
| ----- | ----- | ----- | ----- | ----- |
| Historia Clínica Electrónica (HCE) | Información | Registros clínicos de pacientes: notas de evolución, diagnósticos, prescripciones, resultados de laboratorio e imágenes médicas | Alta | Constituye el núcleo del sistema HCE. Reúne información médica sensible necesaria para la atención sanitaria.  |
| Datos de Registro de Pacientes | Información | Datos personales y demográficos de pacientes: nombre, documento, fecha de nacimiento, contacto | Alta | Contienen información identificatoria y clínica básica del paciente. Su exposición afecta la privacidad y la trazabilidad asistencial.  |
| Notas de Evolución Médica | Información | Notas clínicas ingresadas por médicos sobre la evolución del paciente en cada consulta o internación | Alta | Documentan la evolución, observaciones y decisiones médicas sobre el paciente. Su alteración puede impactar directamente en la atención. |
| Prescripciones de Medicamentos | Información | Órdenes de prescripción médica electrónica; tratamientos farmacológicos, dosis y padecimientos subyacentes | Alta | Contienen indicaciones médicas y tratamientos. Errores, accesos indebidos o modificaciones pueden afectar la seguridad del paciente. |
| Resultados de Laboratorio | Información | Resultados de análisis clínicos, estudios patológicos y biomarcadores de salud. integrados al HCE desde laboratorio externo mediante integración | Alta  | Son datos clínicos usados para diagnóstico y seguimiento. Su pérdida o alteración puede derivar en decisiones médicas incorrectas. |
| Imágenes Médicas (DICOM) | Información | Estudios de imágenes diagnósticas (radiografías, ecografías, tomografías) almacenadas en sistema PACS | alta | Contienen estudios médicos sensibles asociados al paciente. Son críticos para diagnóstico, continuidad asistencial y evidencia clínica.  |
| Credenciales de Acceso del Sistema HCE | Información | Usuarios, contraseñas y tokens de autenticación del personal que accede al sistema HCE | Alta | Su exposición podría habilitar accesos no autorizados, suplantación de identidad, consulta indebida de información clínica o modificación de datos sensibles. |
| Logs de Auditoría del Sistema | Información | Registros de acceso, modificaciones y eventos del sistema HCE; necesarios para trazabilidad y cumplimiento normativo | Alta | Son esenciales para trazabilidad, auditoría, investigación de incidentes y cumplimiento normativo. Si se alteran o eliminan, se pierde evidencia sobre acciones realizadas en la HCE. |

## **Activos de Soporte / Tecnológicos**  **(Contenedores y Canales de la PHI)**  

Estos son los componentes de la arquitectura descrita en el alcance que almacenan, procesan o transmiten los datos de salud y que heredan esa alta criticidad

| Activo | Tipo | Descripción | Criticidad  | Justificación  |
| :---- | ----- | :---- | ----- | ----- |
| Aplicación Web HCE (React) | Software | Frontend de la aplicación de Historia Clínica Electrónica desarrollada en React; interfaz principal de usuario interno   | Medio | Es el punto de interacción con usuarios interno, expone funcionalidades de acceso y consulta de PHI dentro de la red interna.  |
| API REST \+ FHIR | Software | Capa de servicios que expone los datos clínicos según estándar FHIR; esta API transmite activamente datos de salud estructurados entre componentes. | Alta | Procesa y expone datos clínicos mediante servicios. Estructuran el intercambio de datos clínicos. Son críticos para la interoperabilidad, integridad semántica y continuidad asistencial. |
| Base de Datos MySQL | Software | Motor de base de datos relacional que almacena toda la información clínica y administrativa del HCE | Alta | Almacena información clínica y administrativa del sistema.  Si se vulnera afectaría la confidencialidad, integridad y disponibilidad de la HCE. |
| Sistema PACS | Software | Software de gestión y almacenamiento de imágenes médicas DICOM  | Alta (Puede ser medio)  | Gestiona imágenes médicas utilizadas para diagnóstico  |
| Portal para Pacientes | Software | Portal web de acceso para pacientes ingresando por internet; permite consulta de resultados, citas y datos propios de salud | Alta | Permite acceso directo a información clínica sensible. Una falla de autenticación o autorización podría exponer datos médicos.  |
| Módulo de Integración con Laboratorio Externo | Software | Componente de integración que recibe y procesa resultados de laboratorio externo hacia el HCE | Alta | Permite recibir o intercambiar resultados clínicos. Requiere proteger la transmisión y evitar alteraciones o suplantaciones. |

## **Activos Humanos** 

Estos representan a los usuarios, administradores, personal clínico, administrativo y terceros que participan en la operación del sistema HCE. 

| Activo | Tipo | Descripción | Criticidad  | Justificación  |
| :---- | ----- | :---- | ----- | ----- |
| Personal Médico y de Enfermería | Personas | Médicos, enfermeros y demás profesionales de salud que utilizan y operan el sistema HCE | Media (Alta) | Sus acciones impactan directamente en la integridad de la historia clínica, la confidencialidad de los datos del paciente y la continuidad de la atención médica.  |
| Personal de TI / Administradores del Sistema | Personas | Equipo técnico responsable de la administración, mantenimiento y seguridad del sistema HCE | Alta | Estos poseen privilegios elevados sobre la infraestructura, aplicaciones, base de datos, accesos y configuraciones del sistema. Un error, abuso de privilegios o compromiso de estas cuentas podría afectar la disponibilidad, integridad y confidencialidad |
| Personal de Recepción y Registro | Personas | Personal administrativo que registra pacientes y opera módulos administrativos del HCE | Media \- BAja | Estos intervienen en el registro y actualización de datos identificatorios y administrativos de los pacientes. Si bien normalmente no gestiona información clínica profunda, errores o accesos indebidos pueden afectar la calidad del registro, la privacidad del paciente y la correcta vinculación de la información médica. |
| Proveedor Externo (Laboratorio) | Personas | Personal externo del laboratorio con acceso a la integración de datos; riesgo de cadena de suministro | Alta | Estos participan en el intercambio de resultados clínicos con el sistema HCE. Al tratarse de un actor externo, su gestión representa un riesgo de cadena de suministro y puede impactar en la confidencialidad, integridad y disponibilidad de la información diagnóstica. |

#### **Referencias:** 

(1) [https://www.pmg-ssi.com/2016/06/norma-iso-27001-iso-27799-sector-salud/](https://www.pmg-ssi.com/2016/06/norma-iso-27001-iso-27799-sector-salud/)  
(2) [https://www.gub.uy/agencia-gobierno-electronico-sociedad-informacion-conocimiento/comunicacion/publicaciones/guia-implantacion-sistema-gestion-seguridad-informacion-sgsi/sistema](https://www.gub.uy/agencia-gobierno-electronico-sociedad-informacion-conocimiento/comunicacion/publicaciones/guia-implantacion-sistema-gestion-seguridad-informacion-sgsi/sistema)

#### **Glosario:** 

PHI \- Protected Health Information

SGSI \- Sistema de Gestión de Seguridad de la Información

PACS \- Picture Archiving and Communication System

DICOM \- Digital Imaging and Communications in Medicine 

FHIR \- Fast Healthcare Interoperability Resources 