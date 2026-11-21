# Manual técnico — Agenda de tareas (PWA)

## 1. Descripción
Aplicación web progresiva (PWA) para gestionar tareas. Sin frameworks ni dependencias: HTML, CSS y JavaScript puros. Funciona sin conexión y es instalable.

## 2. Estructura de archivos
| Archivo | Función |
|---|---|
| `index.html` | Interfaz, estilos y lógica (todo en un archivo) |
| `manifest.webmanifest` | Metadatos de instalación: nombre, íconos, colores, `display: standalone` |
| `sw.js` | Service Worker: guarda los archivos en caché para uso sin conexión |
| `icon-192.png`, `icon-512.png` | Íconos de la app |

## 3. Arquitectura
- **Datos:** arreglo `tareas` guardado en `localStorage` con la clave `agenda.tareas.v1`. Cada tarea: `{id, titulo, fecha, prio, hecha}`.
- **Renderizado:** la función `render()` reconstruye la lista a partir del arreglo y el filtro activo. Se usa `textContent` (no `innerHTML`) para evitar inyección de código (XSS).
- **Orden:** pendientes primero, luego por fecha límite y por prioridad.
- **Caché:** estrategia *cache-first*; si un archivo no está en caché se pide a la red y se guarda. Sin red, se devuelve `index.html`.
- **Instalación:** el evento `beforeinstallprompt` muestra el botón «Instalar» (Chrome/Edge/Android).

## 4. Requisitos de despliegue
- Servir por **HTTPS** (o `localhost` para pruebas). Los Service Workers no funcionan con `file://`.
- Todos los archivos en la misma carpeta.

## 5. Despliegue
1. **Prueba local:** en la carpeta, ejecuta `python3 -m http.server 8080` y abre `http://localhost:8080`.
2. **Publicación gratuita:** sube la carpeta a GitHub Pages, Netlify, Vercel o Cloudflare Pages (todos dan HTTPS).
3. Verifica en Chrome: DevTools → *Application* → *Manifest* y *Service Workers*, o ejecuta Lighthouse (categoría PWA).

## 6. Mantenimiento
- **Actualizar la app:** cambia el número de `CACHE` en `sw.js` (`agenda-v2`, `agenda-v3`…) cada vez que modifiques archivos; así los usuarios reciben la versión nueva.
- **Cambiar el formato de datos:** cambia la clave de `localStorage` (por ejemplo `agenda.tareas.v2`) y migra los datos antiguos.
- **Cambiar colores:** variables CSS al inicio de `index.html` (`:root`). Incluye modo oscuro automático.

## 7. Limitaciones conocidas
- Los datos viven solo en el dispositivo y navegador: no hay sincronización ni respaldo. Borrar los datos del sitio elimina las tareas.
- No hay notificaciones ni recordatorios.
- En iOS no aparece el botón «Instalar»; se usa *Compartir → Agregar a pantalla de inicio*.

## 8. Mejoras sugeridas
Edición de tareas, exportar/importar JSON, categorías, notificaciones (Push/Notifications API), sincronización con un backend (IndexedDB + API).
