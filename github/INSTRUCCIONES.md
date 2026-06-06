# ♛ EL REY MANDA — Guía de configuración del sistema de licencias
*Seguí estos pasos en orden. Tardás 15-20 minutos en total.*

---

## PASO 1 — Crear cuenta en Supabase (gratis)

1. Entrá a **supabase.com**
2. Hacé clic en **"Start your project"**
3. Registrate con tu cuenta de GitHub o Gmail
4. Hacé clic en **"New project"**
5. Completá:
   - **Name:** el-rey-manda
   - **Database Password:** elegí una contraseña segura (guardala)
   - **Region:** South America (São Paulo)
6. Hacé clic en **"Create new project"**
7. Esperá 1-2 minutos mientras se crea

---

## PASO 2 — Crear la tabla de licencias

1. En el panel de Supabase, hacé clic en **"SQL Editor"** (menú izquierdo)
2. Hacé clic en **"New query"**
3. Copiá y pegá este código SQL exactamente:

```sql
CREATE TABLE licencias (
  id uuid DEFAULT gen_random_uuid() PRIMARY KEY,
  codigo text UNIQUE NOT NULL,
  nombre text,
  email text,
  activo boolean DEFAULT true,
  usado boolean DEFAULT false,
  huella text,
  nombre_activacion text,
  fecha_creacion timestamptz DEFAULT now(),
  fecha_activacion timestamptz,
  notas text
);

-- Permisos para que la app pueda leer y actualizar
ALTER TABLE licencias ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Lectura pública de licencias activas"
  ON licencias FOR SELECT
  USING (activo = true);

CREATE POLICY "Actualización de licencias"
  ON licencias FOR UPDATE
  USING (true);
```

4. Hacé clic en **"Run"** (botón verde o Ctrl+Enter)
5. Debería aparecer "Success. No rows returned."

---

## PASO 3 — Obtener las claves API

1. En el menú izquierdo, hacé clic en **"Project Settings"** (ícono de engranaje)
2. Hacé clic en **"API"**
3. Copiá y guardá estos tres valores:

```
Project URL:     https://XXXXXXXX.supabase.co
anon (public):   eyJhbGc...  (clave larga que empieza con eyJ)
service_role:    eyJhbGc...  (otra clave larga, esta es SECRETA)
```

⚠️ La clave service_role es como tu contraseña maestra. Nunca la compartas públicamente.

---

## PASO 4 — Configurar la app (index.html)

Abrí el archivo **index.html** con cualquier editor de texto (Bloc de notas, TextEdit, VS Code).

Buscá estas líneas cerca del principio del script:

```javascript
const SB_URL  = 'TU_SUPABASE_URL';
const SB_KEY  = 'TU_SUPABASE_ANON_KEY';
```

Reemplazalas con tus valores reales:

```javascript
const SB_URL  = 'https://XXXXXXXX.supabase.co';
const SB_KEY  = 'eyJhbGc...tu_anon_key_aqui...';
```

Guardá el archivo.

---

## PASO 5 — Configurar el panel admin (admin.html)

Abrí el archivo **admin.html** y buscá:

```javascript
const SB_URL     = 'TU_SUPABASE_URL';
const SB_KEY     = 'TU_SUPABASE_ANON_KEY';
const SB_SERVICE = 'TU_SUPABASE_SERVICE_KEY';
const ADMIN_PASS = 'TU_CONTRASEÑA_ADMIN';
```

Reemplazá con tus valores:

```javascript
const SB_URL     = 'https://XXXXXXXX.supabase.co';
const SB_KEY     = 'eyJ...tu_anon_key...';
const SB_SERVICE = 'eyJ...tu_service_role_key...';
const ADMIN_PASS = 'elegí_una_contraseña_segura';
```

Guardá el archivo.

---

## PASO 6 — Subir todo a Netlify

1. Descomprimí el zip del proyecto
2. Entrá a **netlify.com** → tu sitio de El Rey Manda
3. Ir a **Deploys**
4. Arrastrá la carpeta completa `pwa` (que ahora contiene index.html + admin.html + manifest.json + sw.js + los íconos)
5. Esperá que termine el deploy

---

## PASO 7 — Probar el sistema

### Probar la app:
1. Abrí el link de Netlify
2. Debería aparecer la pantalla de **"Activación de Licencia"**
3. Necesitás un código válido para entrar (lo generás en el siguiente paso)

### Probar el panel admin:
1. Abrí **tu-link-netlify.app/admin.html**
2. Ingresá con la contraseña que pusiste en ADMIN_PASS
3. Si ves el panel, ¡todo está funcionando!

---

## PASO 8 — Generar tu primer código de prueba

1. Abrí el panel admin
2. En "Generar nueva licencia":
   - Nombre: tu propio nombre (para prueba)
   - Email: tu email
3. Hacé clic en **GENERAR CÓDIGO**
4. Copiá el código (formato REY-XXXX-XXXX)
5. Abrí la app y activala con ese código
6. ¡Listo!

---

## CÓMO FUNCIONA EL SISTEMA

```
COMPRADOR paga → VOS generás el código en admin.html
               → Le enviás el código por WhatsApp/email
               → El comprador lo ingresa en la app
               → La app verifica con Supabase
               → El código queda BLOQUEADO a ese dispositivo
               → Si alguien más intenta usarlo → RECHAZADO
```

## ACCIONES DISPONIBLES EN EL PANEL ADMIN

| Acción | Qué hace |
|--------|----------|
| **Generar código** | Crea un nuevo código y lo guarda en la base de datos |
| **Desactivar** | Bloquea un código (ej: si hubo un problema con el pago) |
| **Activar** | Reactiva un código desactivado |
| **Reset dispositivo** | Desvincula el código de un dispositivo (ej: si el comprador cambió de celular) |

---

## PRECIOS SUGERIDOS Y MODELO

| Plan | Qué incluye | Precio |
|------|-------------|--------|
| Licencia básica | 1 dispositivo, acceso completo | $ARS 2.500 |
| Licencia premium | 1 dispositivo + actualizaciones futuras | $ARS 4.500 |
| Pack 3 licencias | Para regalar a amigos | $ARS 6.000 |

---

## SOPORTE TÉCNICO

Si algo no funciona, revisá:
1. Que los valores de SB_URL, SB_KEY, SB_SERVICE estén correctos (sin espacios extra)
2. Que el SQL del Paso 2 se ejecutó sin errores
3. Que la carpeta subida a Netlify contiene TODOS los archivos

Ante cualquier duda: socialmediacommu@mail.com

---

*© Carlos Lucas Bertarelli · 2026 · Argentina · Todos los derechos reservados*
