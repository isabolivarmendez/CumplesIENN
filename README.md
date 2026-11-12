# 🎂 Cumpleaños IENN

App para organizar los cumpleaños de la oficina:

- Un calendario con los cumpleaños de todos.
- La lista de deseos y la torta de cada persona. La torta más el regalo suman como máximo $160, y lo ideal es entre $140 y $160.
- En cada cumpleaños, cada quien elige: **regalo y torta**, **solo regalo**, **solo torta** o **no participo**. Cada costo se divide entre los que se anotaron.
- **Club de la torta:** 20 personas vienen anotadas a todas las tortas por defecto (menos la suya), pero pueden salirse cuando quieran.
- **Pasantes** (Alejandro Cifuentes y Luis J Perez): reciben regalo, pero no se anotan a regalar.
- Un chat por cumpleaños para los que participan. **La persona que cumple años nunca ve su grupo ni su chat.**

Funciona con **GitHub Pages** (la página) y **Supabase** (la base de datos y el login). Los dos son gratis.

---

## Puesta en marcha (unos 20 minutos)

### 1. Crear la base de datos en Supabase
1. Entra a https://supabase.com, crea una cuenta y luego un **New project**. El plan gratis alcanza. Guarda la contraseña de la base de datos en algún lado.
2. Ve a **SQL Editor → New query**, pega **todo** el contenido de `supabase/schema.sql` y toca **Run**.
3. Abre otra query nueva, pega `supabase/seed.sql` y toca **Run**. Esto carga las 26 personas del Excel, el club de la torta, los pasantes y el presupuesto de 140–160.

> **¿Ya habías corrido la versión anterior?** No pasa nada: vuelve a correr `schema.sql` y luego `seed.sql`. Los dos se pueden repetir sin duplicar nada ni borrar lo que ya existe.

### 2. Configurar el login
1. Ve a **Authentication → Sign In / Providers → Email**.
2. Deja **Email** activado y **desactiva "Confirm email"**. Guarda.

> ¿Por qué se desactiva? El correo incluido en Supabase solo envía 2 emails por hora, así que la verificación por email no sirve para 26 personas. En lugar de eso, cada persona elige su nombre y **tú apruebas** cada solicitud desde la pestaña Admin. Así nadie puede hacerse pasar por otra persona.

### 3. Conectar la página con Supabase
1. En Supabase, ve a **Project Settings → API** (o **Data API / API Keys**) y copia:
   - la **Project URL** (algo como `https://abcd1234.supabase.co`),
   - la **anon public** key (empieza por `eyJ…`). También puede aparecer como *publishable key*.
2. Abre `index.html`, busca `const CONFIG` cerca del comienzo del script y pega esos dos valores en `supabaseUrl` y `supabaseAnonKey`.

> La anon key es pública por diseño. La seguridad la ponen las reglas de `schema.sql`, que se probaron una por una.

### 4. Publicar en GitHub Pages
1. En https://github.com, crea un repositorio nuevo, por ejemplo `cumples-ienn`. Tiene que ser **Public**: con una cuenta gratis, GitHub Pages solo funciona con repos públicos.
2. Toca **Add file → Upload files** y sube `index.html` y `README.md`. **No subas `supabase/seed.sql`**: tiene los nombres y cumpleaños reales, y el repo es público.
3. Ve a **Settings → Pages → Build and deployment**, elige **Deploy from a branch**, luego `main` y `/ (root)`, y toca **Save**.
4. En uno o dos minutos la página queda en `https://TU-USUARIO.github.io/cumples-ienn/`.

### 5. Hacerte administradora
1. Abre la página, toca **Crear cuenta** con tu email y una contraseña, y elige **Isabella Bolívar** en la lista.
2. En Supabase, ve a **SQL Editor**, pega esto (cambiando el email por el tuyo) y toca **Run**:
   ```sql
   update public.people
   set user_id = (select id from auth.users where email = 'TU_EMAIL@gmail.com'),
       is_admin = true
   where name = 'Isabella Bolívar';

   delete from public.claims
   where user_id = (select id from auth.users where email = 'TU_EMAIL@gmail.com');
   ```
3. Recarga la página. Ya tienes la pestaña **Admin**, donde puedes cambiar el presupuesto y marcar quién está en el club de la torta o es pasante.

### 6. Invitar a todos
Comparte el link. Cada persona crea su cuenta, elige su nombre, y a ti te aparece la solicitud en **Admin → Solicitudes para entrar**. Aprueba solo si el email es de esa persona.

---

## Preguntas frecuentes
- **Alguien olvidó su contraseña:** en Admin, toca **Desvincular** en su fila. La persona crea una cuenta nueva y vuelve a elegir su nombre. Su lista de deseos no se pierde.
- **Cambiar quién está en el club de la torta o quién es pasante:** en Admin, con las casillas 🎂 Club y 🎓 Pasante de cada persona.
- **Agregar a alguien que entra a trabajar:** Admin → formulario de Personas, o **Importar lista** con líneas `Nombre, dd/mm`.
- **Cambiar el nombre "IENN":** en `index.html`, `CONFIG.officeName`.
- **Modo demo:** si `supabaseUrl` está vacío, la app muestra datos de prueba guardados solo en el navegador. Sirve para probarla antes de configurar nada.
