# Servicios Ya — Guía de instalación

## ▶ Paso 1: Subir la app a internet (NECESARIO para PWA)

La app necesita estar en un servidor para instalarse como PWA.
La opción más fácil y GRATIS es **Netlify Drop**:

1. Andá a https://app.netlify.com/drop
2. Arrastrá la CARPETA completa "servicios-ya" a la página
3. En segundos te da una URL como: https://tu-app.netlify.app
4. ¡Listo! Ya podés acceder desde tu celular.

---

## ▶ Paso 2: Configurar Supabase (para datos reales en la nube)

1. Andá a https://supabase.com y creá una cuenta gratis
2. Creá un nuevo proyecto
3. Andá a Settings → API y copiá:
   - **Project URL** (empieza con https://xxxxx.supabase.co)
   - **anon public key** (empieza con eyJ...)
4. Abrí el archivo `index.html` y reemplazá las líneas:
   ```
   const SUPABASE_URL = 'TU_SUPABASE_URL';
   const SUPABASE_KEY = 'TU_SUPABASE_ANON_KEY';
   ```
   con tus credenciales reales.

5. En Supabase → SQL Editor, ejecutá este script para crear las tablas:

```sql
-- Tabla de perfiles de usuario
create table profiles (
  id uuid references auth.users primary key,
  name text,
  role text default 'cliente',
  created_at timestamp default now()
);

-- Habilitar autenticación automática de perfiles
create or replace function public.handle_new_user()
returns trigger as $$
begin
  insert into public.profiles (id, name, role)
  values (new.id, new.raw_user_meta_data->>'name', coalesce(new.raw_user_meta_data->>'role','cliente'));
  return new;
end;
$$ language plpgsql security definer;

create trigger on_auth_user_created
  after insert on auth.users
  for each row execute procedure public.handle_new_user();

-- Tabla solicitudes
create table solicitudes (
  id bigserial primary key,
  user_id uuid references auth.users,
  nombre text not null,
  descripcion text not null,
  monto decimal(10,2) default 0,
  estado text default 'PENDIENTE',
  created_at timestamp default now()
);

-- Tabla citas
create table citas (
  id bigserial primary key,
  user_id uuid references auth.users,
  cliente text not null,
  descripcion text not null,
  fecha text not null,
  hora text not null,
  estado text default 'AGENDADA',
  created_at timestamp default now()
);

-- Tabla conversaciones
create table conversaciones (
  id bigserial primary key,
  de_user uuid references auth.users,
  para_nombre text not null,
  mensajes jsonb default '[]',
  created_at timestamp default now()
);

-- Habilitar Row Level Security
alter table solicitudes enable row level security;
alter table citas enable row level security;
alter table conversaciones enable row level security;

-- Políticas: cada usuario ve solo sus datos
create policy "Ver mis solicitudes" on solicitudes for all using (auth.uid() = user_id);
create policy "Ver mis citas" on citas for all using (auth.uid() = user_id);
create policy "Ver mis convs" on conversaciones for all using (auth.uid() = de_user);
```

---

## ▶ Paso 3: Instalar como app en Android

1. Abrí Chrome en tu celular
2. Navegá a tu URL de Netlify
3. Tocá los 3 puntitos (⋮) del menú de Chrome
4. Seleccioná **"Agregar a pantalla de inicio"**
5. Confirmá → ¡La app aparece en tu home como una app nativa!

---

## ⚡ Sin Supabase (modo demo)

Si no configurás Supabase, la app funciona igual pero:
- Los datos se guardan localmente en el teléfono
- No hay sincronización entre dispositivos
- El login acepta cualquier email/contraseña (demo mode)

---

## 📁 Archivos incluidos

- `index.html` → App completa (toda la lógica y UI)
- `manifest.json` → Configuración PWA
- `sw.js` → Service Worker (modo offline)
- `README.md` → Esta guía
