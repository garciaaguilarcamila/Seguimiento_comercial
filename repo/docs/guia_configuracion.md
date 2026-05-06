# Guía de configuración — Automatización 4: Seguimiento Comercial

## Requisitos previos

- n8n instalado y funcionando (local con Docker o cloud)
- Cuenta de Google
- Google Cloud Project con OAuth2 configurado
- Telegram (opcional)

---

## Paso 1 — Preparar Google Sheets

1. Crea una nueva hoja de cálculo en Google Sheets
2. Nómbrala como quieras (ej: "Seguimiento Comercial Sagatech")
3. Crea una pestaña llamada exactamente **Leads**
4. Añade estas columnas en la fila 1 (una por celda, en este orden):

```
id | nombre | empresa | email | telefono | origen | estado | fase |
fecha_entrada | fecha_ultimo_contacto | fecha_cierre | notas | comercial | intentos
```

5. Puedes importar el archivo `sheets/plantilla_leads.csv` para tener la estructura lista

---

## Paso 2 — Configurar credenciales en Google Cloud

1. Ve a `https://console.cloud.google.com`
2. Selecciona o crea un proyecto
3. Ve a **APIs y servicios → Biblioteca** y habilita:
   - Google Sheets API
   - Google Drive API
   - Gmail API
4. Ve a **APIs y servicios → Credenciales**
5. Crea un ID de cliente OAuth 2.0 de tipo **Aplicación web**
6. En **URIs de redirección autorizados** añade:
   ```
   http://localhost:5678/rest/oauth2-credential/callback
   ```
7. Guarda el **Client ID** y el **Client Secret**

---

## Paso 3 — Configurar credenciales en n8n

### Google Sheets
1. En n8n ve a **Settings → Credentials → Create credential**
2. Busca **Google Sheets OAuth2 API**
3. Pega el Client ID y Client Secret
4. Pulsa **Sign in with Google** y autoriza el acceso

### Gmail
1. Crea una nueva credencial de tipo **Gmail OAuth2 API**
2. Usa los mismos Client ID y Client Secret
3. Autoriza el acceso

### Telegram (opcional)
1. En Telegram busca **@BotFather**
2. Escribe `/newbot` y sigue las instrucciones
3. Guarda el token que te da
4. Escríbele un mensaje a tu bot
5. Ve a `https://api.telegram.org/bot[TOKEN]/getUpdates` y copia tu `chat_id`
6. En n8n crea credencial **Telegram API** y pega el token

---

## Paso 4 — Importar los workflows

1. En n8n ve a **Workflows → Import from file**
2. Importa `workflow/seguimiento_comercial.json`
3. Importa `workflow/reactivacion_leads.json`

---

## Paso 5 — Configurar los nodos

### En el workflow principal, actualiza:

**Todos los nodos Google Sheets:**
- Selecciona tu credencial de Google Sheets
- Pon la URL de tu hoja en el campo Document

**Todos los nodos Gmail:**
- Selecciona tu credencial de Gmail
- Actualiza los emails de destino con los reales

**Todos los nodos Telegram:**
- Selecciona tu credencial de Telegram
- Actualiza el Chat ID con el tuyo

---

## Paso 6 — Ajustar tiempos

Para producción los tiempos recomendados son:
- Primer recordatorio: 24 horas
- Segundo recordatorio: 48 horas
- Cierre por inactividad: 72 horas

Para pruebas reduce todo a 1-2 minutos en los nodos **Wait**.

---

## Paso 7 — Publicar y activar

1. Guarda el workflow
2. Pulsa **Publish**
3. Activa el workflow con el toggle
4. Repite para el workflow de reactivación

---

## Paso 8 — Probar

Añade una fila en Sheets con estos datos mínimos:
```
id:     LEAD-TEST-001
nombre: Prueba Test
email:  tu@email.com
estado: NUEVO
```

Espera 1 minuto y verifica que:
- El estado cambió a EN PROCESO
- Llegó el email de bienvenida
- Los campos fase, fecha_entrada e intentos se rellenaron

Consulta `tests/datos_prueba.json` para los 5 casos de prueba completos.

---

## Solución de problemas frecuentes

| Problema | Causa | Solución |
|---|---|---|
| El trigger no detecta leads | Credencial caducada | Reconectar en Settings → Credentials |
| Los emails no llegan | Token Gmail expirado | Reconectar credencial Gmail |
| Los `$json.nombre` salen vacíos | Falta nodo Get Row antes del Gmail | Añadir Get Row(s) antes del nodo Gmail |
| El lead se procesa varias veces | El Update de EN PROCESO no funciona | Verificar que el campo estado se actualiza correctamente |
| El IF va siempre por False | El estado no coincide exactamente | Verificar mayúsculas y espacios en el estado |
