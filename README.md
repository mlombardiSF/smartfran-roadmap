# SmartFran Roadmap — Guía para Líderes de Proyecto

## Índice

1. [¿Qué es este sistema?](#qué-es-este-sistema)
2. [Estructura de archivos](#estructura-de-archivos)
3. [Cómo agregar un nuevo proyecto](#cómo-agregar-un-nuevo-proyecto)
4. [Cómo agregar una nueva versión anual](#cómo-agregar-una-nueva-versión-anual)
5. [Cómo registrar un release](#cómo-registrar-un-release)
6. [Referencia del JSON de roadmap](#referencia-del-json-de-roadmap)
7. [Referencia del JSON de release](#referencia-del-json-de-release)
8. [Cómo usar las notas desde el frontend](#cómo-usar-las-notas-desde-el-frontend)
9. [Cómo funcionan las notas internamente](#cómo-funcionan-las-notas-internamente)
10. [Preguntas frecuentes](#preguntas-frecuentes)

---

## ¿Qué es este sistema?

El roadmap de SmartFran es una aplicación web estática — **un solo archivo HTML** que carga datos desde archivos JSON alojados en este repositorio. No requiere servidor, base de datos ni backend. GitHub actúa como almacenamiento de datos y GitHub Pages como hosting.

**Lo que cada líder puede hacer:**

- Ver el avance de todos los productos en el dashboard ejecutivo
- Entrar al roadmap de su proyecto y ver el detalle por trimestre
- Consultar el historial de releases desplegados en producción
- Registrar notas y observaciones en cada tarea, con autor y timestamp automático
- Consultar el historial de comentarios de su equipo

**Lo que se administra editando JSON:**

- Agregar o modificar tareas, fechas, responsables y progreso
- Crear nuevas versiones anuales del roadmap
- Registrar releases con sus cambios y tickets asociados
- Incorporar nuevos proyectos al dashboard

---

## Estructura de archivos

```bash
/
├── index.html                      ← La aplicación principal (no modificar)
├── changelog.html                  ← Vista de releases y changelog (no modificar)
├── projects-index.json             ← Índice maestro de proyectos (EDITAR para sumar proyectos)
│
├── assets/
│   └── *.svg, *.png               ← Logos e íconos de los proyectos
│
├── smartfran/
│   ├── roadmap-index.json          ← Versiones disponibles del roadmap
│   ├── releases-index.json         ← Índice de releases del proyecto
│   ├── notes.json                  ← Notas del equipo (se actualiza automáticamente)
│   ├── roadmaps/
│   │   ├── roadmap-2026.json       ← Datos del roadmap 2026
│   │   └── roadmap-2025.json       ← Datos del roadmap 2025 (histórico)
│   └── releases/
│       ├── 2.27.00.json            ← Detalle del release 2.27.00
│       └── 2.26.00.json            ← Detalle del release 2.26.00
│
├── loyalty/
│   ├── roadmap-index.json
│   ├── releases-index.json
│   ├── notes.json
│   ├── roadmaps/
│   │   └── roadmap-2025.json
│   └── releases/
│       └── 1.4.00.json
│
└── pedidos/
    ├── roadmap-index.json
    ├── releases-index.json
    ├── notes.json
    ├── roadmaps/
    │   └── roadmap-2025.json
    └── releases/
        └── 1.2.00.json
```

> **Regla:** cada proyecto vive en su propia carpeta. Los roadmaps anuales van dentro de `roadmaps/` y cada release tiene su propio archivo JSON dentro de `releases/`.

---

## Cómo agregar un nuevo proyecto

Seguí estos 5 pasos en orden.

### Paso 1 — Crear la carpeta del proyecto en el repo

En GitHub, dentro del repo creá la siguiente estructura (reemplazá `miproyecto` con el nombre real en minúsculas sin espacios):

```bash
miproyecto/
├── roadmap-index.json
├── releases-index.json
├── notes.json
└── roadmaps/
    └── roadmap-2025.json
```

Para crear archivos en GitHub: **Add file → Create new file** y escribís el path completo, por ejemplo `miproyecto/roadmaps/roadmap-2025.json`.

### Paso 2 — Crear `roadmap-index.json`

Este archivo lista las versiones anuales disponibles del proyecto. Notar que `file` apunta a la subcarpeta `roadmaps/`.

```json
{
  "versions": [
    {
      "year": 2025,
      "label": "2025 — Actual",
      "file": "roadmaps/roadmap-2025.json",
      "current": true
    }
  ]
}
```

> `current: true` indica cuál es la versión activa. Solo una versión puede tenerlo en `true`.

### Paso 3 — Crear `roadmaps/roadmap-2025.json`

Copiá la estructura base y completá con los datos de tu proyecto. Ver la [referencia completa del JSON](#referencia-del-json-de-roadmap) más abajo.

```json
{
  "meta": {
    "project": "Nombre del Proyecto",
    "version": "2025",
    "description": "Descripción breve del producto",
    "baseYear": 2025,
    "lastUpdated": "2025-06-01"
  },
  "categories": [...],
  "quarters": [...]
}
```

### Paso 4 — Crear `releases-index.json`

Creá el archivo con el array vacío:

```json
{
  "releases": []
}
```

### Paso 5 — Crear `notes.json`

Creá el archivo con contenido vacío:

```json
{}
```

### Paso 6 — Registrar el proyecto en `projects-index.json`

Abrí `projects-index.json` en la raíz del repo y agregá tu proyecto al array `projects`:

```json
{
  "organization": "SmartFran",
  "lastUpdated": "2025-06-01",
  "projects": [
    { ...proyectos existentes... },
    {
      "id": "miproyecto",
      "name": "Nombre visible en el dashboard",
      "color": "#ff6b35",
      "description": "Descripción breve que aparece en la card.",
      "owner": "Nombre del equipo responsable",
      "status": "active",
      "indexFile": "miproyecto/roadmap-index.json"
    }
  ]
}
```

**Campos del proyecto:**

| Campo | Descripción | Ejemplo |
| --- | --- | --- |
| `id` | Identificador único, minúsculas sin espacios | `"miproyecto"` |
| `name` | Nombre visible en el dashboard | `"Mi Proyecto"` |
| `color` | Color hex de identidad del proyecto | `"#ff6b35"` |
| `description` | Texto de la card en el dashboard | `"Gestión de..."` |
| `owner` | Equipo o líder responsable | `"Equipo Producto"` |
| `status` | Estado del proyecto | `"active"` |
| `indexFile` | Ruta al roadmap-index.json | `"miproyecto/roadmap-index.json"` |

Una vez que commitiás los cambios, GitHub Actions hace el deploy automáticamente en ~1 minuto y el nuevo proyecto aparece en el dashboard.

---

## Cómo agregar una nueva versión anual

Cuando empieza un nuevo año (ej: 2027), seguí estos pasos:

### Paso 1 — Crear el archivo del nuevo año

Dentro de `roadmaps/` del proyecto, creá `roadmap-2027.json` con la estructura del nuevo año. Podés partir del año anterior como base.

### Paso 2 — Actualizar `roadmap-index.json`

Agregá la nueva versión y marcá la anterior como `current: false`:

```json
{
  "versions": [
    {
      "year": 2027,
      "label": "2027 — Actual",
      "file": "roadmaps/roadmap-2027.json",
      "current": true
    },
    {
      "year": 2026,
      "label": "2026 — Histórico",
      "file": "roadmaps/roadmap-2026.json",
      "current": false
    }
  ]
}
```

> El selector de versión en el roadmap del proyecto mostrará automáticamente todas las versiones listadas aquí.

---

## Cómo registrar un release

Cuando se despliega una nueva versión a producción, hay que crear el archivo de detalle y registrarlo en el índice del proyecto.

### Paso 1 — Crear el archivo del release

Dentro de `releases/` del proyecto, creá un archivo con el número de versión como nombre (ej: `2.28.00.json`). Ver la [referencia completa](#referencia-del-json-de-release) más abajo.

```json
{
  "version": "2.28.00",
  "date": "2026-07-10",
  "environment": "production",
  "author": "Nombre Apellido",
  "ticketRef": "GSFC-420",
  "summary": "Descripción breve del release.",
  "sections": [
    {
      "title": "Nombre del módulo o área",
      "description": "Qué se trabajó en esta sección.",
      "category": "comercial",
      "items": [
        {
          "ticket": "GSFC-410",
          "title": "Nombre del cambio",
          "description": "Detalle de qué hace este cambio."
        }
      ]
    }
  ]
}
```

### Paso 2 — Actualizar `releases-index.json`

Agregá el nuevo release al principio del array `releases` (orden cronológico descendente):

```json
{
  "releases": [
    {
      "version": "2.28.00",
      "date": "2026-07-10",
      "environment": "production",
      "author": "Nombre Apellido",
      "ticketRef": "GSFC-420",
      "summary": "Descripción breve del release.",
      "file": "smartfran/releases/2.28.00.json"
    },
    { ...releases anteriores... }
  ]
}
```

> El campo `file` debe ser la ruta completa relativa a la raíz del repo.

---

## Referencia del JSON de roadmap

### Estructura completa

```json
{
  "meta": {
    "project": "Nombre del Proyecto",
    "version": "2025",
    "description": "Descripción del roadmap",
    "baseYear": 2025,
    "lastUpdated": "2025-06-01"
  },
  "categories": [
    { "id": "core", "label": "Core & Infra", "color": "#534AB7" }
  ],
  "quarters": [
    {
      "id": "Q1",
      "label": "Q1 2025",
      "status": "done",
      "dateStart": "2025-01-01",
      "dateEnd": "2025-03-31",
      "description": "Descripción del trimestre.",
      "tasks": [
        {
          "id": "q1-01",
          "title": "Nombre de la tarea",
          "description": "Descripción detallada.",
          "category": "core",
          "priority": "alta",
          "status": "done",
          "progress": 100,
          "owner": "Equipo responsable",
          "dateStart": "2025-01-01",
          "dateEnd": "2025-02-28",
          "dateClosedActual": "2025-02-25",
          "notes": "Observación inicial del sistema."
        }
      ]
    }
  ]
}
```

### Valores válidos por campo

**`quarters[].status`**

| Valor | Significado | Apariencia |
| --- | --- | --- |
| `active` | Trimestre en ejecución actual | Violeta — "Ejecutando" |
| `planned` | Planificado, próximo a iniciar | Verde — "Próximo" |
| `backlog` | Largo plazo, sin fecha definida | Gris — "Largo Plazo" |
| `done` | Trimestre completado | Verde — "Completado" |

> Solo un trimestre debería tener `status: "active"` — es el que se abre por defecto al entrar al proyecto.

**`tasks[].status`**

| Valor | Significado |
| --- | --- |
| `in_progress` | En desarrollo activo |
| `review` | En revisión o testing |
| `planned` | Planificada, no iniciada |
| `backlog` | En backlog sin fecha |
| `done` | Completada |

**`tasks[].priority`**

| Valor | Color |
| --- | --- |
| `alta` | Rojo |
| `media` | Ámbar |
| `baja` | Gris |

**`tasks[].progress`**

Número entero de `0` a `100`. Representa el porcentaje de avance.

**`tasks[].dateClosedActual`**

Fecha real de cierre en formato `YYYY-MM-DD`. Si es mayor a `dateEnd`, el Gantt muestra una extensión en rojo indicando el desvío. Si la tarea no cerró aún, dejarlo en `null`.

**`tasks[].notes`**

Texto libre de observación inicial (nota del sistema). Aparece en la card con avatar "Sistema". Se diferencia visualmente de las notas dinámicas del equipo.

**`categories`**

Cada categoría tiene un `id` (referenciado en `tasks[].category`), un `label` visible y un `color` hex. Podés definir las categorías que necesites para tu proyecto.

---

## Referencia del JSON de release

### Estructura de `releases-index.json`

Índice liviano con un objeto por release. Se usa para listar todos los releases en el changelog sin cargar el detalle completo.

```json
{
  "releases": [
    {
      "version": "2.27.00",
      "date": "2026-05-28",
      "environment": "production",
      "author": "Nombre Apellido",
      "ticketRef": "GSFC-394",
      "summary": "Descripción breve del release.",
      "file": "smartfran/releases/2.27.00.json"
    }
  ]
}
```

| Campo | Descripción | Ejemplo |
| --- | --- | --- |
| `version` | Número de versión | `"2.27.00"` |
| `date` | Fecha de deploy en producción | `"2026-05-28"` |
| `environment` | Entorno desplegado | `"production"` |
| `author` | Responsable del release | `"Matias Lombardi"` |
| `ticketRef` | Ticket principal del release | `"GSFC-394"` |
| `summary` | Descripción breve visible en la lista | `"Mejoras en..."` |
| `file` | Ruta al JSON de detalle | `"smartfran/releases/2.27.00.json"` |

### Estructura del archivo de detalle (`releases/X.XX.XX.json`)

Contiene el detalle completo agrupado por secciones funcionales.

```json
{
  "version": "2.27.00",
  "date": "2026-05-28",
  "environment": "production",
  "author": "Nombre Apellido",
  "ticketRef": "GSFC-394",
  "summary": "Descripción breve del release.",
  "sections": [
    {
      "title": "Nombre del módulo o área",
      "description": "Qué se trabajó en esta sección.",
      "category": "comercial",
      "items": [
        {
          "ticket": "GSFC-380",
          "title": "Nombre del cambio",
          "description": "Detalle de qué hace este cambio y su impacto."
        }
      ]
    }
  ]
}
```

**`sections[].category`** — Valor libre que agrupa visualmente los cambios. Ejemplos: `"comercial"`, `"fintech"`, `"infra"`, `"ux"`, `"campanas"`, `"analytics"`.

---

## Cómo usar las notas desde el frontend

Las notas son el mecanismo para registrar el estado real de una tarea durante la ejecución — lo que pasó, lo que se decidió, lo que está bloqueado.

### Agregar una nota

1. Dentro del roadmap de tu proyecto, expandí el trimestre activo haciendo click en la card del Qx
2. En cada tarea, a la derecha del estado, hay un ícono de comentario 💬
3. Hacé click → se abre el modal de comentarios
4. Completá:
   - **Tu nombre** — se muestra como autor del comentario
   - **El texto** — la observación o registro
5. Click en **Registrar nota**

La nota aparece inmediatamente en la card de la tarea con tu nombre, la fecha y hora exacta.

### Ver el historial de notas

El historial completo de comentarios de cada tarea es visible directamente en la card sin necesidad de abrir el modal. Se muestran en orden cronológico:

- **Nota del sistema** — la observación original definida en el JSON (fondo violeta claro)
- **Notas del equipo** — registradas desde el frontend (fondo azul suave), con avatar de iniciales del autor, nombre y timestamp

Para ver el historial completo y agregar una nota nueva, hacé click en el ícono 💬 de la tarea.

### Consideraciones importantes

- **No hay login** — cualquier persona con acceso a la URL puede agregar notas. El nombre es libre, por eso es importante que cada uno escriba su nombre real al registrar.
- **Las notas son permanentes** — una vez guardadas en GitHub no se pueden borrar desde el frontend (requiere editar `notes.json` directamente en el repo).
- **Fecha y hora son automáticas** — se registran en el momento exacto del click en "Registrar nota", con zona horaria local.

---

## Cómo funcionan las notas internamente

Este diagrama muestra el ciclo completo de vida de una nota:

```txt
Usuario escribe nota
        │
        ▼
1. GUARDADO LOCAL (inmediato)
   localStorage del navegador
   Clave: "sf-notes-[proyecto]"
   La nota aparece en pantalla al instante
        │
        ▼
2. SINCRONIZACIÓN GITHUB (automática, en paralelo)
   Lee el SHA actual de [proyecto]/notes.json
   Hace PUT con el JSON actualizado
   Crea un commit automático en el repo
        │
        ▼
3. PRÓXIMA VISITA DE CUALQUIER USUARIO
   loadAllData() al iniciar la app:
     → Fetch de [proyecto]/notes.json desde GitHub
     → Merge con localStorage local
     → GitHub gana en caso de conflicto
   Las notas del equipo aparecen en todas las cards
```

### Merge de notas

Cuando un usuario abre la app, el sistema fusiona las notas de GitHub (fuente de verdad compartida) con las notas en su `localStorage` (pueden tener notas que no llegaron a sincronizarse). La regla es:

- Si una nota está en GitHub → se muestra siempre
- Si una nota está solo en localStorage (ej: falló el sync anterior) → se agrega al merge
- La deduplicación se hace por timestamp — no hay pérdida de datos

### ¿Qué pasa si dos personas escriben al mismo tiempo?

El último en guardar "gana" el archivo en GitHub (last-write-wins). Dado el volumen de uso esperado, la probabilidad de colisión es muy baja. En caso de que ocurra, la nota perdida permanece en el `localStorage` del usuario que la escribió y se recupera en el próximo ciclo de merge al reabrir la app.

---

## Preguntas frecuentes

**¿Puedo editar el progreso de una tarea desde el frontend?**
No. El progreso (`progress: 65`) se actualiza editando el JSON en el repo y commitiando. Esto es intencional — el progreso es un dato de planificación que debe tener trazabilidad de cambios vía Git.

**¿Puedo cambiar el color de mi proyecto?**
Sí, editando el campo `color` en `projects-index.json`. El cambio impacta en el dashboard, el header del roadmap y las barras del Gantt.

**¿Puedo tener más de 4 trimestres?**
Sí. Podés agregar más objetos al array `quarters`. El Gantt se ajusta automáticamente al rango de fechas total.

**¿Cómo pruebo cambios localmente antes de subirlos?**
Con Node instalado, en la carpeta del proyecto:

```bash
npx serve .
```

Abrís `http://localhost:3000`. Los cambios en los JSON se reflejan recargando el browser.

**¿Cómo borro una nota incorrecta?**
Editando directamente `[proyecto]/notes.json` en el repo. El archivo tiene estructura `{ "task-id": [ {author, text, ts}, ... ] }`. Borrás el objeto correspondiente y commitiás.

**¿Puedo pausar o archivar un proyecto sin borrarlo?**
Sí. Cambiá `"status": "active"` a `"status": "paused"` en `projects-index.json`. Por ahora la UI no diferencia visualmente los estados — esto es una mejora futura.

**¿Qué pasa si el deploy de GitHub Actions falla?**
La app sigue funcionando en la versión anterior. Revisá la pestaña **Actions** del repo para ver el log de error.

**¿Puedo editar o borrar un release ya publicado?**
Sí. Editá directamente el archivo `releases/X.XX.XX.json` correspondiente para corregir el contenido, o remové la entrada de `releases-index.json` para que deje de aparecer en el changelog (sin borrar el archivo de detalle). Cualquier cambio requiere un commit.

---

*Para cambios en el diseño o funcionalidad de `index.html` o `changelog.html`, contactar al equipo de desarrollo.*
