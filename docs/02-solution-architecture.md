# Arquitectura de la Solución — Automatización de Reservas Hoteleras

Este documento describe la arquitectura técnica del flujo de trabajo desarrollado en n8n para la gestión, clasificación, registro y respuesta automática de correos sobre reservas hoteleras.

---

## 🔄 Flujo de Datos End-to-End

El proceso opera de manera automatizada de extremo a extremo mediante el siguiente pipeline:

## 🧩 Componentes del Flujo

### 1. Entrada / Trigger (`Gmail Trigger`)
- **Descripción:** Escucha eventos de correos entrantes en la cuenta de Gmail configurada.
- **Función:** Captura en tiempo real las solicitudes de clientes relacionadas con reservas.

### 2. Extracción de Payload (`Get Raw Payload` / `Extraer Datos del Correo`)
- **Descripción:** Procesa el cuerpo del mensaje, asunto, remitente y fecha de recepción.
- **Función:** Limpia el texto y prepara las variables que serán consumidas por el modelo de IA.

### 3. Procesamiento de IA (`Basic LLM Chain` + `Google Gemini Chat Model`)
- **Descripción:** Recibe el texto estructurado del correo y aplica el System Message del archivo `prompt.md`.
- **Función:** Realiza tres tareas clave en un solo paso:
  1. **Clasificación:** Asigna `categoria`, `prioridad`, `idioma` y `confidence`.
  2. **Extracción de datos:** Identifica `numero_reserva`, `checkin`, `checkout`, `hotel` y `nombre_cliente`.
  3. **Generación de respuesta:** Redacta un borrador profesional (`respuesta_sugerida`) adaptado al idioma y contexto del cliente.

### 4. Registro de Datos (`Append row in sheet`)
- **Descripción:** Integración con la API de Google Sheets.
- **Función:** Inserta una nueva fila en la hoja de cálculo de gestión con los metadatos parseados, el estado inicial (`Pending`) y los detalles extraídos para auditoría.

### 5. Creación de Respuesta (`Generar Borrador` + `Create a draft`)
- **Descripción:** Integración de envío/borrador con Gmail.
- **Función:** Toma la `respuesta_sugerida` generada por la IA y crea automáticamente un **Borrador (Draft)** en la cuenta de Gmail dentro del mismo hilo del correo original (`Re:`).

---

## 🛡️ Consideraciones de Diseño y Seguridad

- **Human-in-the-Loop:** El sistema **nunca envía** correos automáticamente al cliente final; únicamente genera borradores para que un agente humano los revise antes del envío.
- **Estructura JSON Estricta:** La salida de la IA está delimitada a un esquema JSON validado para asegurar compatibilidad directa con las columnas de Google Sheets.
- **Soporte Multilingüe:** Manejo automatizado de solicitudes en español e inglés.