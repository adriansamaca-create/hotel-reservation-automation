# Problema de negocio

Los agentes de reservas de cadenas hoteleras reciben un volumen alto de correos con solicitudes heterogéneas (nuevas reservas, cancelaciones, modificaciones, quejas, facturación). Cada correo debe ser leído, clasificado y respondido manualmente, lo que consume tiempo operativo significativo y genera inconsistencia en los tiempos de respuesta, especialmente en operaciones bilingües (inglés/español) con alto volumen.

# Objetivo del proyecto

Diseñar e implementar una plataforma de automatización que reciba los correos entrantes, los clasifique automáticamente por categoría, prioridad e idioma mediante IA, extraiga los datos clave de la reserva y genere un borrador de respuesta, dejando la aprobación final en manos del agente humano.

# Cliente (escenario simulado)

Cadena hotelera ficticia con operación en Norteamérica y Latinoamérica (perfil comparable a Marriott, Hilton o NH Hotels), con un equipo de reservas que atiende correos en inglés y español.

# Alcance del MVP (Minimum Viable Product / Producto Mínimo Viable)

## Incluye

- Recepción automática de correos vía Gmail

- Clasificación por categoría (Cancellation, Modification, Invoice, Upgrade, Complaint, Information), prioridad e idioma

- Extracción de datos estructurados (número de reserva, fechas, cliente, hotel)

- Registro automático en una base de datos (Google Sheets)

- Generación de un borrador de respuesta (guardado como Draft, nunca enviado automáticamente)

## Excluye explícitamente

- Envío automático de correos sin revisión humana

- Integración con sistemas de reservas reales (PMS/CRS)

- Procesamiento de pagos o datos financieros sensibles

# Actores del sistema

- **Cliente:** escribe la solicitud original por correo

- **Sistema IA (n8n + OpenAI):** clasifica, extrae y redacta

- **Agente de reservas:** revisa, corrige si es necesario y aprueba la respuesta final

# Criterios de éxito (medibles en el Día 6 durante testing)

- ≥ 85% de precisión en la clasificación de categoría sobre el set de 40 correos de prueba

- Reducción estimada del tiempo de triage por correo de ~5 minutos (proceso manual) a <1 minuto (revisión de borrador ya generado)

- 0 respuestas enviadas sin revisión humana (control de diseño, no de IA)

# Riesgos y supuestos

- Se asume acceso de solo lectura/borrador a Gmail (sin permisos de envío automático) por diseño, no por limitación técnica

- La calidad de extracción depende de la claridad del correo original; casos ambiguos se enrutan como "requiere revisión manual"