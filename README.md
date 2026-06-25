# UA Flyers — Kit de Materiales Comerciales

Plataforma para que el equipo comercial personalice y descargue flyers de Universal Assistance.

---

## Archivos

| Archivo | Rol |
|---|---|
| `index.html` | Vista comercial: login OTP + galería + editor |
| `admin.html` | Panel admin: subir flyers, gestionar, ver logs |

---

## Setup rápido

### 1. Firebase

1. Crear proyecto en https://console.firebase.google.com
2. Activar **Firestore** (modo producción)
3. Activar **Firebase Storage**
4. Copiar la config del proyecto en ambos archivos (buscar `FIREBASE_CONFIG`)

**Reglas de Storage recomendadas:**
```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /flyers/{allPaths=**} {
      allow read: if true;
      allow write: if request.auth == null; // ajustar si usás Auth
    }
  }
}
```

**Índices de Firestore necesarios:**
- Colección `flyers`: campo `active` ASC + `createdAt` DESC (compuesto)
- Colección `download_logs`: campo `timestamp` DESC

### 2. EmailJS

1. Crear cuenta en https://www.emailjs.com
2. Crear un servicio de email
3. Crear un template con variables `{{to_email}}` y `{{otp_code}}`
4. Completar en `index.html`:
   - `EMAILJS_SERVICE`
   - `EMAILJS_TEMPLATE`
   - `EMAILJS_PUBLIC_KEY`

**Sugerencia de template:**
> Asunto: Tu código de acceso — UA Flyers
>
> Hola,
> Tu código de acceso es: **{{otp_code}}**
> Válido por 10 minutos.

### 3. Constantes a personalizar en `index.html`

```js
const ADMIN_PIN       = "4896";             // PIN del admin
const ALLOWED_DOMAIN  = "@universal-assistance.com"; // dominio permitido
const OTP_EXPIRY_MS   = 10 * 60 * 1000;    // expiración OTP (10 min)
```

---

## Flujo de uso

### Admin
1. Entrar a `index.html` → "Acceso administrador" → PIN `4896`
2. Redirige automáticamente a `admin.html`
3. Subir flyers (JPG/PNG/PDF/PPTX), asignar nombre y categoría
4. Activar/desactivar flyers con el toggle
5. Ver historial de descargas por usuario

### Comercial
1. Entrar a `index.html` → ingresar email `@universal-assistance.com`
2. Recibir código OTP por email, ingresarlo
3. Elegir flyer de la galería
4. Editar campos: título promo, descripción, vigencia, agencia, texto extra, logo partner
5. Descargar PNG (1080×1920px)

---

## Notas técnicas

- Canvas 1080×1920px (Story/Vertical) — ajustable en `renderCanvas()`
- El logo del partner se renderiza arriba a la derecha con fondo blanco
- Cada descarga queda logueada en Firestore (`download_logs`)
- Compatible con GitHub Pages (archivos estáticos)
