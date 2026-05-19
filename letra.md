# 📋 Tareas de Análisis de Riesgos y Modelado de Amenazas

**Duración de la presentación:** 10-15 minutos por grupo  
**Objetivo:** Aplicar metodologías de análisis de riesgos y threat modeling a escenarios reales. Fomentar la propuesta de trabajo de los estudiantes y el intercambio con el docente.
**Metodologías sugeridas:** STRIDE, DREAD, MITRE ATT&CK, NIST, ISO 27001

---

## 📧 Proceso de Inscripción

Cada grupo debe seleccionar un tema y enviar un correo a **apastorini@gmail.com**

**Asunto:** Tarea 2 ISI 2026  
**Copia a:** todos los integrantes  
**Contenido:** Tres escenarios, ordenados por preferencia.

---

## 🗓️ Cronograma

| Fecha | Actividad |
|-------|-----------|
| Mayo (fechas por definir) | Monitoreo en clase - avances, dudas y discusión de opciones con el docente |
| 26 y 28 de mayo | Primera instancia de presentación |
| 16 y 18 de junio | Segunda instancia (solo si el docente lo considera necesario) |

**Criterio de evaluación:** Si el trabajo cumple con los requisitos de entrega, se aprueba la tarea. En caso contrario, el grupo tendrá la posibilidad de una nueva instancia en junio.

---

## 📂 Estructura Git Recomendada (para todos los grupos)

Todo el material generado debe almacenarse en un repositorio Git **público**. Se sugiere la siguiente estructura de carpetas:

```
mi-grupo-riesgos/
├── README.md                    # Descripción del grupo y escenario elegido
├── prompts/                    # (Obligatorio si usan IA) Prompts utilizados
│   ├── 01-inventario-activos.txt
│   ├── 02-stripe-analysis.txt
│   └── 00-memory-bank.md       # Archivo de contexto para la IA (ver sección IA)
├── docs/
│   ├── 01-inventario-activos.md
│   ├── 02-arquitectura-trust-boundaries.md
│   ├── 03-analisis-stride.md
│   ├── 04-priorizacion-dread.md
│   ├── 05-mapa-attack.md
│   ├── 06-plan-mitigacion.md
│   └── 07-riesgos-residuales.md
├── diagrams/
│   ├── arquitectura.drawio
│   ├── arquitectura.png
│   └── attack-flow.png
├── templates/                  # Plantillas utilizadas (copias locales)
│   └── Plantilla_Analisis_Riesgos.md
├── tools/
│   ├── threat-model.xml        # Exportado desde OWASP Threat Dragon / MS TMT
│   └── risk-matrix.xlsx        # Matriz de riesgos (si usa Excel)
└── references/
    └── NIST-SP-800-30.pdf     # Normativas descargadas
```

> **Nota:** Revisar el archivo `Recursos/Plantilla_Analisis_Riesgos.md` para el formato de documentación.

---

## 🤖 Uso de Inteligencia Artificial (Política Obligatoria)

Si el grupo decide utilizar herramientas de IA (Copilot, ChatGPT, Claude, Cursor, etc.), **debe cumplir con los siguientes requisitos**:

### 1. Registro de Prompts
Todos los prompts utilizados deben almacenarse en la carpeta `prompts/` del repositorio.
- Formato sugerido: `01-description.txt` o `01-description.md`
- Deben incluir: fecha, herramienta utilizada y el texto exacto del prompt.

### 2. Memory Bank (Enfoque Sugerido)
Para dar contexto a la IA y obtener mejores resultados, el grupo debe crear un archivo `prompts/00-memory-bank.md`. Este archivo le indica a la IA qué archivos son necesarios para entender el proyecto.

**Ejemplo de `00-memory-bank.md`:**
```markdown
# Memory Bank - Contexto para IA

## Archivos de contexto necesarios:
- `docs/01-inventario-activos.md` - Lista de activos críticos del sistema bancario
- `diagrams/arquitectura.md` - Diagrama de flujo de datos y trust boundaries
- `templates/Plantilla_Analisis_Riesgos.md` - Formato de salida requerido

## Escenario:
Sistema de Banking Digital (Escenario 1). 
Las amenazas deben analizarse bajo la metodología STRIDE aplicada a la arquitectura definida en `diagrams/`.
```

### 3. Declaración de Herramientas
En el `README.md` del repositorio, incluir una sección:
```markdown
## Herramientas de IA utilizadas
- ChatGPT (GPT-4o) - Para generación de borrador de amenazas STRIDE
- Cursor - Asistencia en redacción de controles NIST
```

---

## 📋 Plantillas y Documentación Requerida

Todos los grupos deben basarse en la **Plantilla de Análisis de Riesgos** ubicada en:
`Recursos/Plantilla_Analisis_Riesgos.md`

### Enlaces a Plantillas Oficiales:
| Recurso | Enlace |
|---------|--------|
| **Plantilla Local (Curso)** | `C:\utu\utu\seguridad\Recursos\Plantilla_Analisis_Riesgos.md` |
| **Agesic - Matriz de Riesgos (Excel)** | [Descargar desde Agesic](https://www.gub.uy/agencia-gobierno-electronico-sociedad-informacion-conocimiento/comunicacion/publicaciones/metodologia-gestion-riesgos-v20) |
| **NIST SP 800-30 Rev. 1** | [PDF Nacional (NIST)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-30r1.pdf) |
| **NIST SP 800-53 Rev. 5 (Controles)** | [Controles de Seguridad](https://csrc.nist.gov/pubs/sp/800/53/r5/final) |
| **NIST SP 800-66 (Guía HIPAA)** | [Seguridad en Salud](https://csrc.nist.gov/pubs/sp/800/66/r2/final) |

---

### Escenario 3: Sistema de Salud - Historias Clínicas Electrónicas
**Asignado:** Bibiana Fariello, Paola Benedictti

**Contexto:**
Un hospital implementa un sistema de HCE con:
- Registro de pacientes
- Notas de evolución médica
- Prescripción de medicamentos
- Resultados de laboratorio
- Imágenes médicas (DICOM)
- Portal para pacientes

**Alcance del análisis:**
- Aplicación web (React)
- API REST + FHIR
- Base de datos MySQL
- PACS para imágenes
- Integración con laboratorio externo

**Plantillas y Enlaces de Ayuda:**
- **HIPAA / Salud:** [NIST SP 800-66 Rev. 2](https://csrc.nist.gov/pubs/sp/800/66/r2/final)
- **Privacidad (URCDP):** [Guía Evaluación de Impacto - Agesic](https://www.gub.uy/unidad-reguladora-control-datos-personales/comunicacion/publicaciones/guia-evaluacion-impacto-proteccion-datos-personales)
- **FHIR Security:** [HL7 FHIR Security](https://www.hl7.org/fhir/security.html)
- **ISO 27799:** Gestión de seguridad de la información en salud

**Actividades requeridas:**
1. Identificar activos de alta criticidad (datos de salud = PHI)
2. Cumplir con consideraciones de HIPAA/GDPR
3. Threat modeling para datos médicos
4. Analizar riesgos de interoperabilidad
5. Evaluar controles de acceso a datos sensibles

**Entregables:**
- Risk assessment conforme a NIST SP 800-66
- Análisis de compliance (HIPAA, GDPR)
- Matriz de controles de seguridad
- Privacy impact assessment


---

## 🛠️ Metodologías y Herramientas

### Metodologías a Utilizar

| Metodología | Uso | Herramientas |
|-------------|-----|---------------|
| **STRIDE** | Categorización de amenazas | [Microsoft TMT](https://aka.ms/threatmodelingtool), [OWASP Threat Dragon](https://threatdragon.org/) |
| **DREAD** | Priorización de riesgos | Plantilla propia |
| **MITRE ATT&CK** | Tactics y techniques | [ATT&CK Navigator](https://mitre-attack.github.io/attack-navigator/) |
| **NIST SP 800-30** | Análisis de riesgos | [Plantilla NIST](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-30r1.pdf) |
| **ISO 27001** | Gestión de riesgos | Frameworks ISO |
| **PASTA** | Proceso de threat modeling | Metodología complementaria |

### Herramientas Recomendadas

| Herramienta | Tipo | Descripción | Enlace |
|-------------|------|-------------|--------|
| **OWASP Threat Dragon** | Diagramación | Código abierto para threat modeling | [threatdragon.org](https://www.gub.uy/agencia-gobierno-electronico-sociedad-informacion-conocimiento/comunicacion/publicaciones/guia-implantacion-sistema-gestion-seguridad-informacion-sgsi/anexo-i-0) |
| **Microsoft TMT** | Desktop app | Threat Modeling Tool | [Download](https://aka.ms/threatmodelingtool) |
| **draw.io** | Diagramación | Diagramas de arquitectura | [app.diagrams.net](https://app.diagrams.net/) |
| **MITRE ATT&CK Navigator** | Visualización | Mapeo de amenazas | [attack-navigator](https://mitre-attack.github.io/attack-navigator/) |
| **Risk Radar** | Gestión | Risk register | - |
| **OWASP Risk Rating** | Metodología | Plantilla de riesgos | [OWASP Guide](https://owasp.org/www-project-risk-rating-management/) |

---

## 📊 Criterios de Evaluación

| Criterio | Peso | Descripción |
|----------|------|-------------|
| **Identificación de activos** | 15% | Todos los activos críticos documentados |
| **Diagrama de arquitectura** | 15% | Claro, con trust boundaries |
| **Aplicación de metodología** | 20% | STRIDE/DREAD correctamente usado |
| **Profundidad del análisis** | 20% | Amenazas realistas y completas |
| **Plan de mitigación** | 20% | Controles viables y priorizados |
| **Calidad de documentación** | 10% | Claro y ordenado |

**Nota:** Si se usó IA, se evaluará también la calidad y organización de los prompts en la carpeta `prompts/`.

---

## 🔗 Referencias Generales

| Recurso | URL |
|---------|-----|
| OWASP Threat Modeling | [https://owasp.org/www-community/Threat_Modeling](https://owasp.org/www-community/Threat_Modeling) |
| NIST SP 800-30 Rev. 1 | [https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-30r1.pdf](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-30r1.pdf) |
| NIST SP 800-53 Rev. 5 | [https://csrc.nist.gov/pubs/sp/800/53/r5/final](https://csrc.nist.gov/pubs/sp/800/53/r5/final) |
| MITRE ATT&CK | [https://attack.mitre.org/](https://attack.mitre.org/) |
| ISO 27001 | [https://www.iso.org/isoiec-27001-information-security.html](https://www.iso.org/isoiec-27001-information-security.html) |
| Agesic - Gestión de Riesgos | [https://www.gub.uy/agencia-gobierno-electronico-sociedad-informacion-conocimiento/comunicacion/publicaciones/metodologia-gestion-riesgos-v20](https://www.gub.uy/agencia-gobierno-electronico-sociedad-informacion-conocimiento/comunicacion/publicaciones/metodologia-gestion-riesgos-v20) |
| Plantilla Local | `Plantilla_Analisis_Riesgos.md` |

---