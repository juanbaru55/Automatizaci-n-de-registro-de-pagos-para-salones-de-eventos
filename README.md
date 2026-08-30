# Sistema de Automatización de Pagos — Salón Anselmo

Sistema de automatización con IA para la gestión de pagos de eventos, desarrollado como entrega final del curso **"Ecosistema de Automatización IA Autónomo para Negocios"** (Coderhouse).

## Caso de uso

Salón Anselmo es un salón de eventos real. Este sistema resuelve un problema operativo concreto: los comprobantes de pago llegan por email y hoy se registran a mano. El sistema automatiza ese flujo de punta a punta, con un humano validando cada operación antes de confirmarla.

**Flujo general:**
1. Llega un comprobante de pago por email.
2. Una IA (Gemini) analiza la imagen, valida si es un comprobante real y extrae monto, fecha y método de pago.
3. Si es válido, el sistema busca el evento correspondiente en la base de datos por el email del remitente.
4. Se envía una notificación por Telegram pidiendo aprobación humana (Human-in-the-Loop).
5. Según la respuesta, el pago se registra en la base de datos y se confirma o rechaza por Telegram.

## Stack técnico

- **Orquestador:** [n8n](https://n8n.io/) (Cloud)
- **Base de datos:** [Airtable](https://airtable.com/) — base "Gestión de Pagos de Eventos"
- **IA:** Gemini 3.5 Flash-Lite, vía Google AI Studio
- **Disparador:** Gmail (trigger cada 1 minuto)
- **Aprobación humana / notificaciones:** Telegram Bot

## Estructura de datos (Airtable)

- **Eventos:** datos del cliente, fechas, montos, estado del evento y estado de procesamiento, con saldo pendiente calculado automáticamente.
- **Pagos:** cada pago registrado, vinculado a su evento, con monto, fecha, método y comprobante adjunto.
- **Errores:** registro de fallos del flujo (nodo, motivo, fecha, ID de ejecución) para trazabilidad y debugging.

## Manejo de errores y resiliencia

Los nodos críticos (llamadas a la IA, Airtable y Telegram) tienen reintentos automáticos (3 intentos, 10 segundos de espera) y, si fallan de todas formas, el flujo continúa por una rama de error que notifica por Telegram y deja registro en la tabla Errores — para que ningún fallo pase desapercibido.

## Contenido del repositorio

- `docs/` — documentación del proyecto: diagrama de arquitectura, manual operativo de datos, matriz comparativa de costos de modelos de IA, y documento de seguridad y resiliencia (incluye alcance conocido del sistema).
- `workflow/` — export en JSON del flujo completo de n8n.
- `evidencia/` — capturas en PDF de la interfaz de Airtable (Gestión de Eventos, Procesamiento de Pagos, Dashboard de Control).

## Enlaces

- **Base de datos completa (solo lectura):** https://airtable.com/appoFhoQA5WIoJass/shrjfETvnBDX5Ud5S
- **Dashboard de Control:** no disponible como link en vivo (requiere plan pago de Airtable para compartir la Interface). Ver capturas en `evidencia/Dashboard de Control.pdf`.
- **Video demo:** *[completar con el link una vez subido]*

## Alcance conocido

El sistema procesa comprobantes en formato imagen (no PDF). Este y otros límites de alcance están documentados en detalle en `docs/seguridad_resiliencia.pdf`.
