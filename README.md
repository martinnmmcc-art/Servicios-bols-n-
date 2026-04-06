# Servicios Ya — Firebase Edition
## Auth + Firestore (Storage deshabilitado por ahora)

### ✅ Qué está conectado
- Firebase Auth → login, registro, verificación email
- Firestore → perfiles, solicitudes, citas, mensajes, proyectos, comercios
- Storage → DESHABILITADO (plan Spark). Fotos se guardan como base64 en Firestore.

### 🚀 Setup rápido

1. Firebase Console → Authentication → Activar Email/Password
2. Firebase Console → Firestore → Crear base de datos (modo producción)
3. Firestore → Rules → pegá el contenido de firestore.rules
4. Netlify Drop → arrastrá esta carpeta → obtenés URL pública
5. Chrome Android → abrí la URL → ⋮ → Agregar a inicio

### 📷 Fotos sin Storage

Las fotos se comprimen a base64 (máx 800px, ~100KB) y se guardan en Firestore.
Para foto de perfil: subí tu imagen a imgur.com y pegá la URL al registrarte.

Para activar Storage (cuando estés en plan Blaze):
- Descomentar import firebase-storage en index.html
- La función uploadPhotos() ya está preparada para el cambio

### 👑 Hacer admin
En Firestore → profiles → tu documento → cambiar role a "admin"

### 📁 Archivos incluidos
- index.html — App completa
- manifest.json — Config PWA
- sw.js — Soporte offline
- firestore.rules — Reglas de seguridad
- README.md — Esta guía
