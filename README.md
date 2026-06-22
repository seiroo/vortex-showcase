<img width="1983" height="793" alt="img" src="https://github.com/user-attachments/assets/48b16cdc-4419-4117-a30b-d7c7680c7943" />


#  Vortex

**Gestión inteligente de issues de GitHub con tableros Kanban y automatización impulsada por IA.**

Vortex es una aplicación web full-stack que transforma la gestión de proyectos en GitHub en una experiencia visual, colaborativa y automatizada. Conecta tus repositorios, organiza issues en columnas personalizables, y deja que la IA te ayude a generar, priorizar y dar seguimiento a las tareas.


---

##  ¿Qué hace Vortex?

- **Tableros Kanban en tiempo real** — Arrastra y suelta issues entre columnas, con actualizaciones instantáneas vía WebSockets.
- **Integración profunda con GitHub** — Sincronización bidireccional: los cambios en Vortex se reflejan en GitHub y viceversa.
- **IA generativa** — Genera issues automáticamente a partir de descripciones en lenguaje natural usando DeepSeek.
- **Multi-espacio de trabajo** — Gestiona múltiples proyectos y repositorios desde una sola interfaz.
- **Colaboración en equipo** — Asigna responsables, etiquetas, comentarios y seguimiento de cambios.
- **Historial de auditoría** — Cada modificación queda registrada con trazabilidad completa.
- **Filtros avanzados** — Búsqueda por texto, etiquetas, responsables y estados.
- **Diseño responsive** — Experiencia optimizada para escritorio y dispositivos móviles.

---

##  Stack Tecnológico

| Capa | Tecnología |
|------|-----------|
| **Framework** | Next.js 16 (App Router) + React 19 |
| **Lenguaje** | TypeScript |
| **Estilos** | Tailwind CSS v4 |
| **Autenticación** | Supabase Auth (OAuth con GitHub) |
| **Estado del servidor** | TanStack React Query v5 |
| **Estado de UI** | Zustand v5 |
| **Íconos** | Lucide React + FontAwesome |
| **Virtualización** | TanStack React Virtual (renderizado eficiente de miles de tarjetas) |
| **Testing unitario** | Vitest + Testing Library |
| **Testing e2e** | Playwright |
| **Linting** | ESLint 9 (configuración plana) |
| **CI/CD** | GitHub Actions |

### Servicios externos

| Servicio | Propósito |
|----------|----------|
| **Supabase** | Base de datos PostgreSQL, autenticación y suscripciones en tiempo real |
| **GitHub API** | Sincronización de issues, repositorios y metadatos |
| **DeepSeek** | Motor de IA para generación de issues |
| **Backend propio (Java)** | API REST que orquesta la lógica de negocio y el acceso a datos |

---

##  Arquitectura

```
┌──────────┐      ┌──────────────────┐      ┌─────────────┐
│ Navegador │ ───► │  Vortex Frontend  │ ───► │ Backend API  │
│ (Cliente)  │ ◄─── │ (Next.js + React)  │ ◄─── │ (Java REST)   │
└──────────┘      └──────────────────┘      └──────┬───────┘
      │                     │                      │
      │                     │ Supabase Auth        │ service_role
      │                     │ (GitHub OAuth)       │ (bypass RLS)
      │                     │                      │
      ▼                     ▼                      ▼
┌──────────────────────────────────────────────────────────┐
│                   Supabase (PostgreSQL)                   │
│  • Datos de columnas, issues, comentarios, etiquetas     │
│  • Suscripciones en tiempo real (CDC)                     │
└──────────────────────────────────────────────────────────┘
```

### Principios de diseño

- **Seguridad** — El frontend nunca consulta la base de datos directamente. Toda operación pasa por el backend, que valida permisos con JWT.
- **Tiempo real** — Las actualizaciones se propagan a todos los clientes conectados mediante los canales `postgres_changes` de Supabase.
- **Rendimiento** — Virtualización de listas para manejar cientos de tarjetas sin degradación. Caché agresiva con React Query.
- **Responsive** — La interfaz se adapta a móviles con navegación optimizada y menús contextuales táctiles.

---

##  Estructura del Proyecto

El proyecto sigue una arquitectura modular basada en el App Router de Next.js, organizada por dominio y responsabilidad:

```
├── app/                # Páginas y rutas (App Router de Next.js)
├── components/         # Componentes de UI reutilizables (tablero, modales, menús, filtros)
├── hooks/              # Hooks personalizados (autenticación, operaciones CRUD, notificaciones)
├── lib/                # Lógica de negocio y servicios
│   ├── backend/        #   Cliente tipado para la API REST
│   ├── queries/        #   Gestión de estado del servidor (React Query)
│   └── ai/             #   Integración con servicios de IA generativa
├── stores/             # Estado global de UI (Zustand)
├── e2e/                # Tests end-to-end (Playwright)
├── test/               # Tests unitarios (Vitest)
└── public/             # Assets estáticos
```

---

##  Funcionalidades destacadas

###  Tablero Kanban

- Columnas personalizables con estados configurables
- Drag & drop entre columnas con indicadores visuales
- Creación y edición de issues in-line
- Soporte para teclado (navegación con flechas, atajos)
- Vista de detalle con comentarios, asignación y etiquetas

###  IA Generativa

- Genera múltiples issues a partir de una descripción en lenguaje natural
- Plantillas inteligentes basadas en el contexto del proyecto
- Flujo guiado: seleccionas repositorio, escribes la idea, la IA propone los issues

###  Autenticación

- Login con GitHub OAuth a través de Supabase
- Sesiones persistentes con refresh automático
- Sincronización de permisos con el backend

###  Colaboración

- Asignación de issues a miembros del equipo
- Sistema de etiquetas con colores personalizables
- Comentarios con edición y eliminación
- Indicadores de seguimiento (watch/unwatch)

###  Búsqueda y filtros

- Filtrado por texto, etiquetas, responsables
- Búsqueda con debounce para rendimiento
- Filtros persistentes en la sesión

###  Experiencia móvil

- Navegación inferior con accesos rápidos
- Menús desplegables optimizados para táctil
- Sidebar colapsable como overlay

---

##  Testing

El proyecto cuenta con una suite de tests completa:

| Tipo | Herramienta | Cobertura |
|------|------------|-----------|
| **Unitarios** | Vitest + Testing Library | Stores, hooks, utilidades, contenido de issues |
| **End-to-End** | Playwright | Flujos completos: landing page y tablero Kanban |

```bash
npm test              # Tests unitarios
npm run test:e2e      # Tests end-to-end
npm run test:e2e:ui   # Tests e2e con interfaz visual
npm run ci            # Pipeline completo (lint + build + test)
```

---

##  Seguridad

- **Content Security Policy (CSP)** — Headers restrictivos configurados en `next.config.ts`
- **X-Content-Type-Options**, **X-Frame-Options**, **X-XSS-Protection** — Headers de seguridad activos
- **Referrer-Policy** y **Permissions-Policy** — Configuración explícita
- **JWT validation** — Cada petición al backend es autenticada y autorizada
- **Row Level Security** — El backend usa `service_role` para bypassear RLS de forma controlada
- **Secretos** — Las credenciales sensibles nunca se almacenan en el frontend

---

##  Licencia

The source code of Vortex is proprietary and remains private.
This repository serves as a public showcase of the product architecture, features and development approach.

---

*Desarrollado con ❤️ usando Next.js, React, Supabase y TypeScript.*
