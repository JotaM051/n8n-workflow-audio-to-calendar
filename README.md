# Bot de WhatsApp que agenda audios en Google Calendar

Le mandas un audio por WhatsApp (por ejemplo, "Agéndame reunión con el profesor el jueves a las 3 de la tarde") y el bot crea el evento en tu Google Calendar y te responde con una confirmación.

## Cómo funciona

1. Twilio (WhatsApp Sandbox) recibe tu audio y llama a un webhook de n8n.
2. n8n verifica que el mensaje venga de tu número y descarga el audio.
3. Gemini escucha el audio y devuelve título, fecha, hora, duración y notas en JSON.
4. n8n crea el evento en Google Calendar.
5. El bot responde por WhatsApp con la confirmación (nodo Respond to Webhook).

## Servicios que usa

- n8n (Cloud o self-hosted)
- Twilio WhatsApp Sandbox
- Google Gemini API (Google AI Studio)
- Google Calendar

## Cómo instalarlo

1. Descarga `workflow-audio-a-calendar.json` e impórtalo en n8n (menú ⋯ → Import from file).
2. Crea una API key de Gemini en aistudio.google.com y pégala en el nodo "Gemini entiende el audio" (parámetro `key`).
3. Crea una cuenta en Twilio y activa el WhatsApp Sandbox. Envía el código `join ...` desde tu WhatsApp.
4. En n8n crea una credencial Basic Auth con tu Account SID (usuario) y tu Auth Token (contraseña) y selecciónala en el nodo "Descargar audio".
5. En el nodo "Solo mi numero con audio" escribe tu número con el formato `whatsapp:+506XXXXXXXX`.
6. Conecta tu cuenta de Google en el nodo "Crear evento en Calendar".
7. Usa un nodo "Respond to Webhook" con cuerpo `<Response><Message>...</Message></Response>` y header `Content-Type: text/xml` para confirmar por WhatsApp. En el nodo Webhook, configura Respond como "Using 'Respond to Webhook' Node".
8. Publica el workflow, copia la Production URL del Webhook y pégala en Twilio (Sandbox settings → When a message comes in, método POST).

## Uso

Envía un audio al número del sandbox indicando qué agendar, el día y la hora.

- Sin hora: se crea como evento de todo el día.
- Sin fecha: se agenda para hoy.
- Duración por defecto: 1 hora.

## Limitaciones

- El sandbox de Twilio se desvincula cada 72 horas; hay que reenviar el código `join ...`.
- Twilio espera la respuesta del webhook unos 15 segundos.
- La transcripción puede fallar con nombres propios o audios con ruido.

## Seguridad

No subas tu API key de Gemini, tu Auth Token de Twilio ni tu número personal a este repositorio. Antes de publicar el JSON, reemplaza esos valores por texto de relleno.
