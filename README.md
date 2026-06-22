# Koré · Panel interno

Sitio web de uso interno para el equipo de Koré. Permite:

- **Ver promociones vigentes** (con estado automático: vigente, por vencer, programada, vencida).
- **Ver la agenda de capacitaciones** (fecha, modalidad, cupos y ocupación).
- **Anotar personas** a cada capacitación y **descargar la lista en CSV**.
- **Cargar promociones** y **agendar capacitaciones** desde formularios.

Es un único archivo `index.html` sin dependencias ni paso de compilación: se sube tal cual.

---

## 1. Subirlo a GitHub Pages (gratis, ~5 minutos)

1. Creá un repositorio nuevo en GitHub, por ejemplo `kore-panel`.
2. Subí el archivo `index.html` a la raíz del repo (botón **Add file → Upload files**, arrastrás el archivo, **Commit changes**).
3. Andá a **Settings → Pages**.
4. En **Source** elegí la rama `main` y la carpeta `/ (root)`. Guardá.
5. Esperá ~1 minuto. GitHub te da una URL del tipo `https://tu-usuario.github.io/kore-panel/`.

Esa URL es el panel. La compartís con el equipo y listo.

> **Privacidad:** GitHub Pages es público (cualquiera con el link entra). Para uso interno alcanza con no difundir la URL, pero **no cargues datos sensibles de clientes** ahí. Si necesitás acceso restringido de verdad, mirá la opción de Firebase con reglas de acceso (abajo) o un hosting con login (Netlify/Vercel con password).

---

## 2. ⚠️ Importante: dónde se guardan los datos

Tal como viene, el panel guarda todo **en el navegador de cada persona** (localStorage). Eso significa:

- ✅ Funciona al instante, sin configurar nada, sin costo.
- ✅ Sirve para probar el flujo completo y tomar decisiones de diseño.
- ❌ **No se comparte entre personas ni entre computadoras.** Si Flor carga una promo en su notebook, la sucursal de Paraná **no la ve**. Cada quien ve su propia copia.

Para una herramienta interna real (varios empleados, 4 sucursales viendo lo mismo) hace falta una **base de datos compartida**. Abajo está el upgrade, que es lo único que falta para que sea multiusuario.

---

## 3. Upgrade a datos compartidos (recomendado): Firebase

[Firebase Firestore](https://firebase.google.com/) tiene un plan gratuito que alcanza de sobra para este uso, no requiere servidor propio y funciona con sitios estáticos como GitHub Pages.

**Pasos generales:**

1. Creá un proyecto gratis en [console.firebase.google.com](https://console.firebase.google.com).
2. Activá **Firestore Database** (modo de prueba para empezar).
3. En *Configuración del proyecto → Tus apps → Web*, copiá el objeto `firebaseConfig`.
4. En `index.html`, dentro del `<head>`, agregá los SDK y tu config:

```html
<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/10.12.0/firebase-app.js";
  import { getFirestore, collection, getDocs, addDoc, deleteDoc, doc }
    from "https://www.gstatic.com/firebasejs/10.12.0/firebase-firestore.js";

  const app = initializeApp({ /* pegá acá tu firebaseConfig */ });
  window.koreDB = getFirestore(app);
</script>
```

5. Reemplazá el objeto `Store` del script por una versión que lea/escriba en Firestore (3 colecciones: `promos`, `capas`, `inscriptos`). La estructura de cada registro ya está documentada en el código (campos `titulo`, `desde`, `hasta`, `sucs`, etc.), así que el reemplazo es directo. Si querés, te lo dejo escrito y listo para pegar.

> **Alternativa más familiar para el equipo:** usar **Google Sheets como base** (vía Apps Script). Las inscripciones caen directo en una planilla que la community manager ya sabe manejar. Es un poco más de configuración inicial pero deja los datos en un lugar conocido. También te lo puedo armar.

---

## 4. Marca y personalización

- El **verde Koré, tipografías y espaciados** están definidos como variables CSS al inicio del archivo (bloque `:root`). Tu diseñador/a cambia un valor y se actualiza todo el panel.
- Las **marcas** (`MARCAS`) y **sucursales** (`SUCURSALES`) están en constantes al inicio del `<script>`; se editan en un renglón.
- Los datos de ejemplo (promos y capacitaciones de muestra) están en la función `seed()` y aparecen solo la primera vez. Con datos reales cargados, podés borrar ese bloque.

---

## 5. Qué decidir antes de usarlo en serio

1. **¿localStorage o base compartida?** Para uso real con varias personas, sí o sí base compartida (sección 3).
2. **¿Quién carga promos y capacitaciones?** Hoy cualquiera con el link puede. Si querés separar "ver" de "cargar", se agrega una clave simple o login.
3. **¿Dónde viven las inscripciones?** Si van a un CRM/email a futuro (como prevé el plan B2B), conviene que la base compartida sea la misma que después alimenta esa herramienta.
