# Control de errores y logs — Seguimiento Comercial

## Errores frecuentes y soluciones

### Error: "The provided authorization grant is invalid"
**Causa:** Token OAuth2 de Google o Gmail caducado  
**Solución:** Ir a n8n → Settings → Credentials → abrir la credencial afectada → Reconnect  
**Nodos afectados:** Cualquier nodo de Google Sheets o Gmail

---

### Error: "No output data returned" en Get Row(s)
**Causa 1:** El filtro no encuentra ninguna fila con ese valor  
**Causa 2:** El campo `id` referenciado trae datos del nodo anterior (Gmail/Telegram) en vez de Sheets  
**Solución:** Verificar que el Value del filtro usa `{{ $('NombreNodoSheets').item.json.id }}` en lugar de `{{ $json.id }}` cuando hay nodos Gmail o Wait entre medias  

---

### Error: "Invalid interval" al publicar
**Causa:** El nodo Schedule Trigger tiene el intervalo vacío o en 0  
**Solución:** Abrir el nodo → verificar que Minutes Between Triggers tiene un número válido (mínimo 1)  

---

### El lead se procesa varias veces (emails duplicados)
**Causa:** El nodo Update Row no está actualizando el estado a EN PROCESO correctamente  
**Solución:**  
1. Verificar que el nodo Update Row está antes del Gmail de bienvenida  
2. Verificar que el campo `estado` tiene el valor `EN PROCESO` configurado  
3. Verificar que el match por `id` está funcionando (ejecutar el nodo manualmente y ver el output)  

---

### El IF siempre va por la rama False aunque el estado coincide
**Causa:** Diferencia de mayúsculas, espacios o caracteres invisibles entre el valor en Sheets y el valor en el IF  
**Solución:**  
1. Usar `.trim()` en la expresión: `{{ $json.estado.trim() }}`  
2. O cambiar el operador a `contains` en lugar de `is equal to`  
3. Verificar que el Update Row escribe exactamente el mismo texto que el IF compara  

---

### Los parámetros `$json.nombre` aparecen vacíos en el email
**Causa:** El nodo Gmail recibe datos de un Update Row que no tiene todos los campos del lead  
**Solución:** Añadir un nodo Get Row(s) justo antes del Gmail para recuperar todos los datos del lead  

---

### Telegram no envía mensajes
**Causa 1:** El bot no ha recibido ningún mensaje previo del usuario (no ha iniciado conversación)  
**Causa 2:** El chat_id es incorrecto  
**Solución:**  
1. Abrir Telegram y escribirle al bot  
2. Ir a `https://api.telegram.org/bot[TOKEN]/getUpdates` para obtener el chat_id correcto  

---

## Cómo revisar las ejecuciones en n8n

1. En n8n ve a **Executions** (menú lateral)
2. Verás todas las ejecuciones con su estado (verde = OK, rojo = error)
3. Haz clic en cualquier ejecución para ver en qué nodo falló
4. El nodo con error aparece en rojo con el mensaje de error en el panel derecho

---

## Log de acciones en Google Sheets

El workflow registra estas acciones en la columna `notas` de cada lead:

| Acción | Valor en notas |
|---|---|
| Lead atendido antes del primer recordatorio | "Atendido antes del primer recordatorio" |
| Lead atendido tras primer recordatorio | "Atendido tras primer recordatorio" |
| Lead atendido tras escalado | "Atendido tras escalado" |
| Lead cerrado por inactividad | "Cerrado automáticamente por inactividad" |
| Lead reactivado manualmente | "Lead reactivado manualmente" |

---

## Columnas de seguimiento en Sheets

Estas columnas se actualizan automáticamente por el workflow:

| Columna | Cuándo se actualiza |
|---|---|
| `estado` | En cada cambio de fase |
| `fase` | Al avanzar de fase |
| `fecha_entrada` | Al detectar el lead por primera vez |
| `fecha_ultimo_contacto` | Cada vez que se envía un aviso |
| `fecha_cierre` | Al cerrar por inactividad |
| `intentos` | Cada vez que se envía un aviso al comercial |
| `notas` | Al registrar atención o cierre |
