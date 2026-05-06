# 🤖 Automatización 4 — Seguimiento Comercial Automático

> **Sagatech** · Automatizaciones para empresas reales

Secuencia automática de follow-up para leads en múltiples fases. El sistema detecta contactos nuevos, envía el primer email, lanza recordatorios progresivos al comercial por Gmail y Telegram, escala si no hay respuesta y cierra por inactividad. Todo queda registrado en Google Sheets sin intervención manual.

---

## ❓ Qué problema resuelve

Las PYMEs pierden entre un 30% y 60% de sus leads por falta de seguimiento. El comercial se olvida, llega tarde o no tiene un proceso definido. Esta automatización garantiza que **ningún lead quede sin atender**.

## ⏱️ Ahorro de tiempo

| Tarea | Manual | Automatizado |
|---|---|---|
| Revisar leads nuevos | 2h/semana | 0 min |
| Enviar emails de contacto | 3h/semana | 0 min |
| Recordar hacer seguimiento | 1h/semana | 0 min |
| Actualizar estado en CRM | 2h/semana | 0 min |
| **Total** | **8h/semana** | **~15 min revisión** |

---

## 🔄 Flujo de trabajo

```
Lead entra (estado NUEVO)
  ↓
Marca como EN PROCESO
  ↓
Email de bienvenida al lead
  ↓
Espera 24h ──→ ¿Atendido? ──→ SÍ → Registra OK
  ↓ NO
Avisa comercial (Gmail + Telegram)
  ↓
Espera 48h ──→ ¿Atendido? ──→ SÍ → Registra OK
  ↓ NO
Aviso URGENTE al responsable
  ↓
Estado → ESCALADO
  ↓
Espera 72h ──→ ¿Atendido? ──→ SÍ → Registra OK
  ↓ NO
Cierre automático por inactividad
```

---

## 📋 Estados del lead

| Estado | Descripción |
|---|---|
| `NUEVO` | Lead recién añadido |
| `EN PROCESO` | Flujo iniciado |
| `ESCALADO` | Sin atender tras 72h |
| `CERRADO` | Cerrado por inactividad |
| `ATENDIDO` | Atendido por el comercial |
| `REACTIVADO` | Lead cerrado que se reactiva |

---

## 🛠️ Tecnologías utilizadas

![n8n](https://img.shields.io/badge/n8n-EA4B71?style=for-the-badge&logo=n8n&logoColor=white)
![Google Sheets](https://img.shields.io/badge/Google_Sheets-34A853?style=for-the-badge&logo=google-sheets&logoColor=white)
![Gmail](https://img.shields.io/badge/Gmail-EA4335?style=for-the-badge&logo=gmail&logoColor=white)
![Telegram](https://img.shields.io/badge/Telegram-2CA5E0?style=for-the-badge&logo=telegram&logoColor=white)

---

## 📦 Estructura del repositorio

```
automatizacion-4-seguimiento-comercial/
├── 📁 workflow/
│   ├── Seguimiento Comercial.json    ← workflow principal (importar en n8n)
│   └── reactivacion_leads.json       ← workflow de reactivación
├── 📁 sheets/
│   └── plantilla_leads.csv           ← estructura de la hoja de Sheets
├── 📁 templates/
│   └── plantillas_mensajes.txt       ← textos de emails y Telegram
├── 📁 tests/
│   └── datos_prueba.json             ← 5 casos de prueba documentados
├── 📁 docs/
│   ├── guia_configuracion.md         ← configuración paso a paso
│   ├── guia_replicacion.md           ← cómo replicarlo en otro cliente
│   └── control_errores_logs.md       ← errores frecuentes y soluciones
├── config.example.json               ← configuración de ejemplo
├── .gitignore
└── README.md
```

---

## 🚀 Instalación rápida

```bash
# 1. Clona el repositorio
git clone https://github.com/garciaaguilarcamila/Seguimiento_comercial.git

# 2. Copia y rellena la configuración
cp config.example.json config.json
```

Luego:
1. Importa los dos workflows en n8n
2. Crea la hoja de Sheets con `sheets/plantilla_leads.csv`
3. Configura las credenciales (ver `docs/guia_configuracion.md`)
4. Activa ambos workflows
5. Añade un lead con estado `NUEVO` para probar

---

## 💰 Versiones y precio orientativo

| Versión | Incluye | Precio |
|---|---|---|
| **Mínima** | Sheets + Gmail + 1 recordatorio | 350 — 500 € |
| **Completa** | + Telegram + 3 fases + escalado + reactivación | 800 — 1.200 € |
| **Premium** | + Panel métricas + Google Form de entrada | 1.500 — 2.000 € |
| **Mantenimiento** | Soporte mensual | 50 — 80 €/mes |

---

## 🎯 Tipo de cliente objetivo

- PYMEs con equipo comercial de 1 a 5 personas
- Negocios que reciben leads por web, redes sociales o ferias
- Empresas que tardan más de 24h en responder a sus contactos
- Sectores: inmobiliaria, clínicas, academias, consultoras, agencias

---

## 📄 Documentación

- [Guía de configuración](docs/guia_configuracion.md)
- [Guía de replicación para nuevo cliente](docs/guia_replicacion.md)
- [Control de errores y logs](docs/control_errores_logs.md)

---

## 👩‍💻 Desarrollado por

**Sagatech** — Automatizaciones para empresas reales  
Proyecto académico · Curso de automatización con n8n
