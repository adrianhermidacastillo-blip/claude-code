# Estafy · valoración de viviendas + portal de inmobiliarias

Web en español para captar vendedores de vivienda en España. Node (ESM) + Express, sin base de datos (archivos JSON). Todo el front es HTML/JS plano, sin build.

## Estructura
- `server.js`: API y estáticos. Escáner de fotos (`/api/escanear`, Claude), leads (`/api/lead`), panel (`/api/admin/*`, cabecera `x-admin`), portal de inmobiliarias (`/api/agencias/*`, token Bearer en memoria).
- `public/index.html`: web pública (valoración por zona/barrio con datos de Fotocasa e idealista, teléfono obligatorio para ver el precio, ventana «Vender mi casa», dirección obligatoria, mensaje de contacto en 24 h, escáner, cuestionario de estado, textos legales, cookies, menú).
- `public/admin.html`: panel del propietario (clientes, contrato firmado, cesión, asignar inmobiliaria, pestaña Inmobiliarias).
- `public/agencias.html`: registro, login y portal de inmobiliarias.
- `Dockerfile`, `render.yaml`: despliegue en Render con disco persistente (`/data`).

## Variables de entorno
`ADMIN_PASSWORD` (obligatoria para /admin), `ANTHROPIC_API_KEY` (escáner), `MODEL`, `LIMITE_POR_HORA`, `LEADS_FILE`, `AGENCIAS_FILE`, `LEAD_WEBHOOK_URL`, `PORT`. Ver `.env.example`.

## Arrancar
`npm install && npm start` → http://localhost:3000, panel en `/admin`, inmobiliarias en `/agencias.html`. Sin `ANTHROPIC_API_KEY` el servidor arranca y solo se desactiva el escáner.

## Flujo de negocio
Visitante valora (teléfono) → pide venta (nombre, teléfono, dirección, cesión a inmobiliaria) → el propietario marca «Contrato firmado» y «Consiente cesión» → el cliente aparece, sin contacto, a las inmobiliarias aprobadas cuyas zonas coinciden → «Quedármelo» lo asigna a una (o lo asigna el propietario) y revela los datos de contacto.

## Pendiente (nada de esto se ha podido probar de extremo a extremo)
1. Probar el servidor de verdad: registrar inmobiliaria, aprobarla, marcar cliente firmado, reclamarlo, escaneo con fotos reales. Escribir pruebas automáticas (supertest o similar) de `/api/lead`, `/api/admin/*` y `/api/agencias/*`.
2. Rellenar datos reales: `AGENCIA` y `LEGAL` (razón social, NIF, domicilio, registro, honorarios) al final de `public/index.html`; `EMPRESA` y condiciones económicas en `public/agencias.html`.
3. Revisión legal por un abogado (aviso legal, condiciones, privacidad, cookies, condiciones de colaboración) y comprobar requisitos de registro de agentes inmobiliarios de la comunidad autónoma.
4. Self-hostear la fuente de Google Fonts (envía la IP del visitante a Google) o mantener el aviso en la política de cookies.
5. Avisos: hoy el propietario debe mirar `/admin`; considerar email o webhook (`LEAD_WEBHOOK_URL`) para leads y altas de inmobiliarias. La promesa «contacto en 24 horas» depende de ello.
6. Mejoras posibles: sesiones persistentes del portal, recuperar contraseña de inmobiliarias, subir documentos de acreditación, migrar a SQLite/Postgres si crece el volumen.

## Notas de seguridad
Datos personales en `leads.json` y `agencias.json` (no subir a git; ya están en `.gitignore`). Contraseñas de inmobiliarias con scrypt. Límite de intentos en admin y login. Las salidas se pintan con `textContent` (sin `innerHTML` con datos de usuario). Mantener ese criterio.

## Primer prompt sugerido
«Lee CLAUDE.md y README.md. Instala dependencias, arranca el servidor con una ADMIN_PASSWORD de prueba, escribe pruebas automáticas de los endpoints y corrige lo que falle.»
