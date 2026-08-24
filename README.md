# 🏨 Hotel Reservation Operations Automation Platform

> Automatización del triage, clasificación y respuesta de correos de reservas hoteleras usando IA generativa — construido con n8n + Google Gemini + Gmail + Google Sheets.

\---

## 🎯 Problema de negocio

Los agentes de reservas en cadenas hoteleras reciben un alto volumen de correos con solicitudes heterogéneas: cancelaciones, modificaciones, quejas, upgrades y consultas generales. El proceso manual implica:

* **11 minutos promedio** por correo (lectura, clasificación, búsqueda de datos, redacción y registro)
* Riesgo de error humano en la priorización
* Respuestas inconsistentes según el agente disponible
* Pérdida de oportunidades comerciales por saturación operativa

\---

## ✅ Solución

Una plataforma de automatización que intercepta cada correo entrante, lo clasifica con IA, extrae los datos clave y genera un borrador de respuesta profesional — todo en menos de 30 segundos, con revisión humana antes del envío.

\---

## 🏗️ Arquitectura

### AS-IS — Proceso Manual

!\[Diagrama AS-IS](docs/Diagrama\_AS-IS\_\_Manual.png)

### TO-BE — Proceso Automatizado

!\[Diagrama TO-BE](docs/Diagrama\_TO-BE\_Automatizado.png)

\---

## ⚙️ Workflow de Producción

!\[Workflow n8n](assets/workflow-screenshot.png)

|Nodo|Función|
|-|-|
|Gmail Trigger|Detecta correos entrantes en tiempo real|
|Get Raw Payload|Extrae cuerpo completo (Simplify OFF)|
|Extraer Datos del Correo|Prepara asunto, remitente, cuerpo y fecha|
|Basic LLM Chain + Gemini|Clasifica y extrae datos estructurados|
|Structured Output Parser|Valida el JSON de respuesta|
|If|Enruta según confidence y categoría|
|Append row in sheet|Registra en Google Sheets|
|Generar Borrador|Redacta respuesta profesional con IA|
|Create a draft|Guarda borrador en Gmail sin enviar|

\---

## 🤖 Stack Tecnológico

|Herramienta|Uso|
|-|-|
|[n8n](https://n8n.io)|Orquestador de workflows (Docker local)|
|Google Gemini (`gemini-3.1-flash-lite`)|Clasificación y generación de texto|
|Gmail API (OAuth2)|Trigger y creación de borradores|
|Google Sheets API (OAuth2)|Base de datos de solicitudes|
|Google Cloud Platform|Gestión de credenciales OAuth2|

\---

## 📊 Categorías de Clasificación

|Categoría|Descripción|
|-|-|
|Cancellation|Solicitudes de cancelación de reserva|
|Modification|Cambios de fecha, habitación o datos|
|Invoice|Solicitudes de factura o comprobante|
|Upgrade|Solicitudes de mejora de habitación|
|Complaint|Quejas formales por servicio|
|Information|Consultas generales e informativas|
|Revisión Manual|Correos ambiguos o no clasificables|

\---

## 🔀 Lógica de Enrutamiento (Human-in-the-Loop)

```
confidence < 0.9        → Revisión Manual
categoria = "Unknown"   → Revisión Manual  
categoria = "Revisión Manual" → Revisión Manual
Todo lo demás           → Borrador automático → Revisión humana → Envío
```

**Decisión de diseño:** ningún correo se envía automáticamente. El agente siempre revisa y aprueba el borrador antes del envío.

\---

## 📈 Resultados del Testing

|Métrica|Resultado|
|-|-|
|Total de correos procesados|93|
|Clasificaciones con alta confianza (≥ 0.9)|93.5%|
|Correos enrutados a revisión manual|8.6%|
|Extracción correcta de nombre del cliente|73.1%|
|Extracción correcta de número de reserva|55.9%|
|Soporte multilingüe|Español + Inglés|
|Tiempo estimado ahorrado por correo|\~8.4 min|
|Tiempo total ahorrado (93 correos)|\~13 horas|

> Ver métricas completas en \[`metrics.md`](metrics.md) y análisis detallado en \[`docs/05-testing-results.md`](docs/05-testing-results.md)

\---

## 📁 Estructura del Repositorio

```
hotel-reservation-automation/
├── docs/
│   ├── 01-business-requirements.md
│   ├── 02-solution-architecture.md
│   ├── 05-testing-results.md
│   ├── Diagrama\_AS-IS\_\_Manual.png
│   └── Diagrama\_TO-BE\_Automatizado.png
├── assets/
│   └── workflow-screenshot.png
├── sample\_emails/
├── workflow/
├── prompt.md
├── metrics.md
└── README.md
```

\---

## 🚀 Cómo ejecutar el proyecto

### Requisitos

* Docker Desktop instalado
* Cuenta de Google Cloud con Gmail API y Sheets API habilitadas
* API Key de Google Gemini (gratuita)

### Pasos

**1. Levantar n8n con Docker:**

```bash
docker run -it --rm --name n8n -p 5678:5678 \\
  -v n8n\_data:/home/node/.n8n \\
  docker.n8n.io/n8nio/n8n
```

**2. Importar el workflow:**

* Abre `http://localhost:5678`
* Importa el archivo JSON desde la carpeta `workflow/`

**3. Configurar credenciales:**

* Gmail OAuth2
* Google Sheets OAuth2
* Google Gemini API Key

**4. Activar el workflow** y enviar un correo de prueba al inbox configurado.

\---

## 🧠 Aprendizajes clave

* **Migración de modelos Gemini:** `gemini-2.0-flash` → `gemini-2.5-flash` → `gemini-3.1-flash-lite` por deprecaciones y límites de rate (20 RPD → 500 RPD)
* **LLM output siempre es string:** usar `JSON.parse($json.text).campo` en lugar de `$json.campo`
* **Separador decimal en Windows:** n8n hereda la configuración regional — usar `{{ 0.9 }}` como expresión JS en lugar de valor numérico directo
* **Simplify OFF es crítico:** el nodo Gmail con Simplify ON trunca el cuerpo del correo
* **Human-in-the-Loop por diseño:** Gmail Draft en lugar de envío automático — reduce riesgo y genera confianza operativa

\---

## 🔮 Mejoras Futuras

* Integración con PMS (Property Management System) para validar números de reserva en tiempo real
* Dashboard en Google Sheets con métricas automáticas por categoría y agente
* Notificaciones por Slack para correos de alta prioridad
* Soporte para más idiomas (portugués, francés)
* Fine-tuning del prompt por tipo de hotel (boutique vs. cadena corporativa)

\---

## 👤 Autor

**Adrian Gonzalez**  
AI Automation Specialist  
[LinkedIn](https://linkedin.com/in/adriansamaca) · [GitHub](https://github.com/adriansamaca-create)

\---

*Proyecto desarrollado en 7 días como portafolio técnico para posiciones de AI Automation Specialist.*

