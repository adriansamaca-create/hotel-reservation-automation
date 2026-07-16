# hotel-reservation-automation

AI-powered automation platform for hotel reservation email processing using n8n, Gemini and Google Sheets

## Progreso técnico

- ✅ n8n corriendo localmente vía Docker
- ✅ OAuth de Gmail y Google Sheets configurado (Google Cloud)
- ✅ Workflow funcional: Gmail Trigger → extracción de asunto, remitente, cuerpo y fecha

### Capturas

!Workflow Gmail Trigger(screenshots/dia2-workflow-gmail-trigger.png)
!Resultado de extracción(screenshots/dia2-resultado-extraccion.png)

## Día 3 — Integración con IA

- ✅ Conectado Google Gemini (`gemini-3-flash-preview`) vía API key
- ✅ Prompt de clasificación diseñado con rol, reglas, categorías y ejemplos few-shot
- ✅ Salida JSON estructurada y validada con "Structured Output Parser"
- ✅ Workflow completo: Gmail Trigger → Extracción → Clasificación con IA (categoría, prioridad, idioma, confidence)

### Captura

!Clasificación JSON con Gemini](screenshots/dia3-clasificacion-json.png)