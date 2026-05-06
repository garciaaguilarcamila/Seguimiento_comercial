# Guía de replicación para nuevo cliente

## Tiempo estimado de implementación: 2-3 horas

Esta guía está pensada para replicar la automatización en un cliente nuevo
partiendo desde cero. Sigue los pasos en orden.

---

## Checklist de inicio

Antes de empezar confirma con el cliente:

- [ ] ¿Tiene cuenta de Google? (Gmail + Sheets)
- [ ] ¿Quiere avisos por Telegram además de email?
- [ ] ¿Cuántos comerciales hay? (uno o varios)
- [ ] ¿Cuánto tiempo quiere esperar entre recordatorios?
- [ ] ¿Tiene n8n instalado o hay que instalarlo?
- [ ] ¿Cómo llegan los leads actualmente? (web, teléfono, redes...)

---

## Fase 1 — Preparación (30 min)

### 1.1 Crear la hoja de Sheets del cliente

1. Crea una hoja nueva en el Google Drive del cliente
2. Nómbrala: `[NombreEmpresa] - Seguimiento Comercial`
3. Crea la pestaña **Leads** con las columnas del proyecto
4. Comparte la hoja con el email del Google Cloud del cliente

### 1.2 Configurar Google Cloud del cliente

Si el cliente no tiene proyecto en Google Cloud:
1. Entra en `console.cloud.google.com` con la cuenta del cliente
2. Crea proyecto nuevo con el nombre de la empresa
3. Habilita Sheets API, Drive API y Gmail API
4. Crea credencial OAuth2 con la URI de redirección de su n8n

Si el cliente ya tiene proyecto:
1. Verifica que las APIs necesarias están habilitadas
2. Añade la URI de redirección de su n8n a las credenciales existentes

---

## Fase 2 — Instalación en n8n (45 min)

### 2.1 Importar workflows

1. Accede al n8n del cliente (`http://[IP]:5678`)
2. Importa `workflow/seguimiento_comercial.json`
3. Importa `workflow/reactivacion_leads.json`
4. Renombra ambos workflows con el nombre del cliente

### 2.2 Crear credenciales

Crea estas credenciales en el n8n del cliente:

| Nombre sugerido | Tipo | Datos necesarios |
|---|---|---|
| `[Cliente] Google Sheets` | Google Sheets OAuth2 | Client ID + Secret |
| `[Cliente] Gmail` | Gmail OAuth2 | Client ID + Secret |
| `[Cliente] Telegram` | Telegram API | Bot Token |

### 2.3 Actualizar nodos

Recorre todos los nodos del workflow y actualiza:

**Nodos Google Sheets (todos):**
- Credencial → seleccionar la del cliente
- Document URL → URL de la hoja del cliente

**Nodos Gmail (todos):**
- Credencial → seleccionar la del cliente
- To → email del comercial del cliente
- From → email comercial del cliente

**Nodos Telegram (todos):**
- Credencial → seleccionar la del cliente
- Chat ID → chat_id del comercial del cliente

### 2.4 Ajustar tiempos según el cliente

Modifica los nodos Wait según lo acordado:
```
Wait 1: horas acordadas para primer recordatorio
Wait 2: horas acordadas para segundo recordatorio
Wait 3: horas acordadas para cierre por inactividad
```

---

## Fase 3 — Pruebas (45 min)

Ejecuta los 5 casos de prueba del archivo `tests/datos_prueba.json`
con los datos reales del cliente (usar emails de prueba del cliente).

### Checklist de pruebas

- [ ] Caso 1: Lead atendido antes del primer recordatorio
- [ ] Caso 2: Lead que necesita primer recordatorio
- [ ] Caso 3: Lead que escala a urgente
- [ ] Caso 4: Lead cerrado por inactividad
- [ ] Caso 5: Reactivar un lead cerrado

Para cada caso verifica:
- [ ] Email de bienvenida llega al lead
- [ ] Aviso llega al comercial por Gmail
- [ ] Aviso llega al comercial por Telegram
- [ ] La hoja de Sheets se actualiza correctamente
- [ ] Los estados cambian en el orden correcto

---

## Fase 4 — Formación al cliente (30 min)

Explica al cliente:

1. **Cómo añadir un lead**: rellenar la fila en Sheets con estado NUEVO
2. **Cómo marcar un lead como atendido**: cambiar estado a ATENDIDO en Sheets
3. **Cómo reactivar un lead cerrado**: cambiar estado a REACTIVADO en Sheets
4. **Qué significan los avisos de Telegram**: cada mensaje y cuándo llega
5. **Qué hacer si falla algo**: contactar con Sagatech

---

## Fase 5 — Entrega (15 min)

Entrega al cliente:

- [ ] Acceso al n8n con los workflows activos
- [ ] Link a la hoja de Sheets
- [ ] Este documento adaptado con sus datos
- [ ] El `config.example.json` relleno con sus valores reales (sin contraseñas)
- [ ] Contacto de soporte

---

## Personalización habitual por cliente

| Personalización | Dónde se hace | Tiempo |
|---|---|---|
| Texto de los emails | Nodos Gmail en n8n | 15 min |
| Tiempos de espera | Nodos Wait en n8n | 5 min |
| Añadir más comerciales | Duplicar nodos de aviso | 20 min |
| Conectar formulario web | Añadir nodo Webhook como trigger | 30 min |
| Panel de métricas en Sheets | Pestaña adicional con fórmulas | 45 min |

---

## Notas de mantenimiento

- Los tokens OAuth2 de Google caducan. Si el cliente reporta que los emails no llegan, reconectar las credenciales en n8n → Settings → Credentials
- Si se añaden columnas nuevas a la hoja de Sheets, actualizar los nodos Get Row y Update Row del workflow
- Revisar las ejecuciones en n8n → Executions una vez por semana durante el primer mes
