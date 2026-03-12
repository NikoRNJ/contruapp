# 🚀 PLAN ANTIGRAVITY — Contruapp

> **Documento maestro de planificación, arquitectura e instrucciones para el agente de desarrollo.**
> Este archivo es la fuente de verdad para construir Contruapp de principio a fin.

---

## 📋 Índice

1. [Visión del Proyecto](#-visión-del-proyecto)
2. [Stack Tecnológico](#-stack-tecnológico)
3. [Arquitectura General](#-arquitectura-general)
4. [Modelo de Datos (MER)](#-modelo-de-datos-mer)
5. [Fases del Proyecto](#-fases-del-proyecto)
6. [Fase 1 — MVP Backend (Issues #1-#6)](#fase-1--mvp-backend-issues-1-6)
7. [Fase 2 — Frontend Mobile-First](#fase-2--frontend-mobile-first)
8. [Fase 3 — Funcionalidades Avanzadas](#fase-3--funcionalidades-avanzadas)
9. [Fase 4 — App Nativa (PWA / React Native)](#fase-4--app-nativa-pwa--react-native)
10. [Reglas y Convenciones del Agente](#-reglas-y-convenciones-del-agente)
11. [Estructura del Proyecto](#-estructura-del-proyecto)
12. [Instrucciones de Setup](#-instrucciones-de-setup)
13. [Checklist General](#-checklist-general)

---

## 🎯 Visión del Proyecto

**Contruapp** es una plataforma **mobile-first** diseñada para que trabajadores de la construcción en Chile puedan crear su perfil profesional y ser encontrados por clientes potenciales. Es el "LinkedIn de la construcción chilena".

### Problema que resuelve
Los trabajadores de la construcción (albañiles, electricistas, gasfíters, etc.) no tienen una forma digital sencilla de mostrar su trabajo y ser contactados. Los clientes no tienen dónde buscar profesionales confiables por zona y especialidad.

### Propuesta de valor
- Cada trabajador tiene un **perfil público** con foto, nombre, experiencia y portafolio fotográfico
- Los clientes pueden **buscar y filtrar** por oficio, ubicación (región/comuna/ciudad) y edad
- La plataforma **solicita ubicación** para mostrar trabajadores cercanos
- **Sin login** para ver perfiles en la web; **con login** solo si se descarga la app

### Usuarios objetivo
| Tipo | Acción |
|---|---|
| **Trabajador** | Crea perfil, sube fotos, se da a conocer |
| **Cliente/Buscador** | Busca trabajadores por oficio y zona |

---

## 🛠 Stack Tecnológico

| Capa | Tecnología | Propósito |
|---|---|---|
| **Frontend** | Next.js 14+ (App Router) | Web mobile-first, SSR, SEO |
| **Estilos** | Tailwind CSS | UI rápida, responsiva |
| **Lenguaje** | TypeScript | Tipado estricto, menos bugs |
| **Backend/DB** | Supabase (PostgreSQL) | Base de datos, Auth, Storage, API REST |
| **Auth** | Supabase Auth | Login/registro con email/password |
| **Storage** | Supabase Storage | Fotos de perfil y portafolio |
| **Deploy** | Vercel | Hosting web con CI/CD automático |
| **App futura** | PWA o React Native / Expo | App descargable para móvil |

---

## 🏗 Arquitectura General

```
┌─────────────────────────────────────────────────────────┐
│                    CLIENTE (Browser/App)                  │
│               Next.js 14 + Tailwind CSS                  │
│                    Mobile-First                           │
│                                                          │
│  ┌────────────────────────────────────────────────────┐  │
│  │  PÁGINA PRINCIPAL (Home)                           │  │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐          │  │
│  │  │ 📸 Foto  │ │ 📸 Foto  │ │ 📸 Foto  │  ← Grid │  │
│  │  │ Nombre   │ │ Nombre   │ │ Nombre   │  3 cols  │  │
│  │  │ Oficio   │ │ Oficio   │ │ Oficio   │          │  │
│  │  └──────────┘ └──────────┘ └──────────┘          │  │
│  │                                                    │  │
│  │  🔍 [Buscador: nombre / categoría]                │  │
│  │  📍 [Filtros: región, comuna, ciudad, edad]       │  │
│  └────────────────────────────────────────────────────┘  │
│                                                          │
│  RUTAS:                                                  │
│  /              → Home (grid de trabajadores + filtros)   │
│  /worker/[id]   → Perfil público del trabajador          │
│  /login         → Login (solo app descargada)            │
│  /register      → Registro de trabajador                 │
│  /dashboard     → Panel del trabajador (editar perfil)   │
└──────────────────────┬───────────────────────────────────┘
                       │ Supabase Client SDK
                       │ (REST API + Realtime)
┌──────────────────────▼───────────────────────────────────┐
│                      SUPABASE                             │
│                                                          │
│  ┌─────────────────────────────────────────────────────┐ │
│  │  PostgreSQL Database                                │ │
│  │                                                     │ │
│  │  ┌────────────┐  ┌─────────────┐  ┌─────────────┐ │ │
│  │  │ professions│  │   regions   │  │   comunas   │ │ │
│  │  │ (catálogo) │  │ (16 Chile)  │  │ (por región)│ │ │
│  │  └────────────┘  └─────────────┘  └──────┬──────┘ │ │
│  │                                           │        │ │
│  │  ┌────────────────────────────────────────┘        │ │
│  │  │                                                 │ │
│  │  ▼                                                 │ │
│  │  ┌─────────────┐     ┌──────────────┐             │ │
│  │  │   workers   │────▶│  work_photos │             │ │
│  │  │  (perfiles) │     │ (portafolio) │             │ │
│  │  └──────┬──────┘     └──────────────┘             │ │
│  │         │                                          │ │
│  │         ▼                                          │ │
│  │  ┌─────────────┐                                  │ │
│  │  │ auth.users  │  ← Supabase Auth                 │ │
│  │  └─────────────┘                                  │ │
│  └─────────────────────────────────────────────────────┘ │
│                                                          │
│  🔐 Auth: email/password (solo app)                      │
│  📦 Storage: profile-photos, work-photos (buckets)       │
│  🛡️ RLS: Row Level Security en todas las tablas          │
└──────────────────────────────────────────────────────────┘
```

---

## 💾 Modelo de Datos (MER)

### Tablas y Relaciones

```
┌──────────────────┐       ┌──────────────────┐
│   professions    │       │     regions      │
├──────────────────┤       ├──────────────────┤
│ id (PK, UUID)    │       │ id (PK, UUID)    │
│ name (UNIQUE)    │       │ name (UNIQUE)    │
│ category         │       │ roman_numeral    │
│ created_at       │       │ created_at       │
└────────┬─────────┘       └────────┬─────────┘
         │ 1                        │ 1
         │                          │
         │ N                        │ N
┌────────▼─────────┐       ┌────────▼─────────┐
│     workers      │       │     comunas      │
├──────────────────┤       ├──────────────────┤
│ id (PK, UUID)    │       │ id (PK, UUID)    │
│ user_id (FK)     │◄──────│ name             │
│ name             │       │ city             │
│ email (UNIQUE)   │       │ region_id (FK)   │
│ phone            │       │ created_at       │
│ age              │       └──────────────────┘
│ profession_id(FK)│
│ experience_years │
│ experience_desc  │
│ profile_photo_url│
│ comuna_id (FK)   │
│ latitude         │
│ longitude        │
│ is_public        │
│ created_at       │
│ updated_at       │
└────────┬─────────┘
         │ 1
         │
         │ N
┌────────▼─────────┐
│   work_photos    │
├──────────────────┤
│ id (PK, UUID)    │
│ worker_id (FK)   │
│ photo_url        │
│ description      │
│ created_at       │
└──────────────────┘
```

### Relaciones
| Origen | Destino | Tipo | Descripción |
|---|---|---|---|
| `workers.profession_id` | `professions.id` | N:1 | Cada trabajador tiene un oficio |
| `workers.comuna_id` | `comunas.id` | N:1 | Cada trabajador pertenece a una comuna |
| `comunas.region_id` | `regions.id` | N:1 | Cada comuna pertenece a una región |
| `work_photos.worker_id` | `workers.id` | N:1 | Cada foto pertenece a un trabajador |
| `workers.user_id` | `auth.users.id` | 1:1 | Cada trabajador tiene una cuenta auth |

### Seguridad (RLS)
| Tabla | SELECT | INSERT | UPDATE | DELETE |
|---|---|---|---|---|
| `professions` | ✅ Público | ❌ | ❌ | ❌ |
| `regions` | ✅ Público | ❌ | ❌ | ❌ |
| `comunas` | ✅ Público | ❌ | ❌ | ❌ |
| `workers` | ✅ Público (is_public=true) | 🔐 Owner | 🔐 Owner | 🔐 Owner |
| `work_photos` | ✅ Público | 🔐 Owner del worker | 🔐 Owner del worker | 🔐 Owner del worker |

---

## 📅 Fases del Proyecto

### Fase 1 — MVP Backend (Issues #1-#6)
> **Estado: 🔄 EN PROGRESO**

| Issue | Skill | Estado | Entregables |
|---|---|---|---|
| [#1](https://github.com/NikoRNJ/contruapp/issues/1) | Épico MVP | 🔄 | Issue padre |
| [#2](https://github.com/NikoRNJ/contruapp/issues/2) | Modelo MER | 🔄 | `docs/MER.md` |
| [#3](https://github.com/NikoRNJ/contruapp/issues/3) | Scripts Supabase | 🔄 | `supabase/migrations/*.sql` |
| [#4](https://github.com/NikoRNJ/contruapp/issues/4) | Profesiones Chile | 🔄 | `docs/PROFESIONES_CHILE.md` |
| [#5](https://github.com/NikoRNJ/contruapp/issues/5) | Auth/Login | 🔄 | `src/lib/auth.ts`, `src/lib/supabase.ts` |
| [#6](https://github.com/NikoRNJ/contruapp/issues/6) | Filtros/Consulta | 🔄 | `src/lib/workers.ts`, `src/lib/filters.ts` |

#### Instrucciones detalladas para cada skill:

**Skill #2 — MER (`docs/MER.md`)**
- Documentar el diagrama entidad-relación en ASCII art
- Describir cada tabla con sus campos, tipos y constraints
- Documentar todas las relaciones FK
- Incluir políticas RLS por tabla

**Skill #3 — Scripts SQL (`supabase/migrations/`)**
- `001_create_tables.sql`: CREATE TABLE con UUID, índices, RLS habilitado y políticas
- `002_seed_professions.sql`: INSERT de 22+ profesiones con categoría
- `003_seed_regions_comunas.sql`: INSERT de 16 regiones + 80+ comunas principales
- Todos los scripts deben ser idempotentes (usar IF NOT EXISTS donde sea posible)

**Skill #4 — Profesiones (`docs/PROFESIONES_CHILE.md`)**
- Lista completa categorizada con descripción de cada oficio
- Jerarquía: Jornal → M2 → M1 → Capataz → Supervisor
- Fuentes: Código del Trabajo, CChC, convenios colectivos

**Skill #5 — Auth (`src/lib/auth.ts`)**
- signUp(email, password, workerData) → crea auth user + perfil worker
- signIn(email, password) → login
- signOut() → cerrar sesión
- getCurrentUser() → sesión actual
- resetPassword(email) → recuperación
- Todo tipado con TypeScript, manejo de errores

**Skill #6 — Filtros (`src/lib/workers.ts` + `src/lib/filters.ts`)**
- getWorkers(filters?) → query builder dinámico con paginación
- getWorkerById(id) → perfil completo con joins
- searchWorkers(query) → búsqueda por nombre con ilike
- createWorkerProfile(data) → requiere auth
- updateWorkerProfile(id, data) → requiere auth, solo owner
- uploadProfilePhoto(file) → Supabase Storage bucket 'profile-photos'
- uploadWorkPhoto(workerId, file, description?) → bucket 'work-photos'
- getProfessions() → catálogo ordenado
- getRegions() → 16 regiones
- getComunasByRegion(regionId) → comunas filtradas

---

### Fase 2 — Frontend Mobile-First
> **Estado: ⏳ PENDIENTE** (se ejecuta después de Fase 1)

| Componente | Descripción | Ruta |
|---|---|---|
| **HomePage** | Grid 3 columnas de cards de trabajadores, buscador superior, filtros | `/` |
| **WorkerCard** | Card con foto, nombre, oficio, comuna | Componente |
| **SearchBar** | Buscador por nombre o categoría con autocomplete | Componente |
| **FilterPanel** | Filtros: profesión (select), región (select), comuna (select), edad (range) | Componente |
| **WorkerProfile** | Página de perfil público: foto, datos, experiencia, galería de trabajos, mapa | `/worker/[id]` |
| **LoginPage** | Formulario email/password (solo visible en app descargada) | `/login` |
| **RegisterPage** | Registro de trabajador con datos básicos | `/register` |
| **Dashboard** | Panel del trabajador: editar perfil, subir fotos, ver estadísticas | `/dashboard` |
| **LocationPermission** | Popup para solicitar permiso de geolocalización | Componente |
| **Layout** | Header con logo + navegación, Footer | Componente |

#### Especificaciones UI:
- **Mobile-first**: Diseñar primero para 375px, luego escalar
- **Grid de cards**: 1 columna en móvil, 2 en tablet, 3 en desktop
- **Colores**: Paleta inspirada en construcción (naranjas, grises, azules oscuros)
- **Tipografía**: Inter o similar, legible en pantallas pequeñas
- **Iconos**: Heroicons o Lucide React
- **Imágenes**: Optimización con next/image, lazy loading
- **Accesibilidad**: ARIA labels, contraste WCAG AA mínimo

---

### Fase 3 — Funcionalidades Avanzadas
> **Estado: ⏳ PENDIENTE**

| Feature | Descripción |
|---|---|
| **Reseñas y valoraciones** | Sistema de estrellas (1-5) + comentario de clientes |
| **Verificación de perfil** | Badge de "verificado" con documento de identidad |
| **Notificaciones** | Email/push cuando alguien ve tu perfil |
| **Chat interno** | Mensajería entre cliente y trabajador |
| **Favoritos** | Guardar trabajadores favoritos (requiere cuenta) |
| **Estadísticas** | Dashboard con vistas del perfil, contactos recibidos |
| **SEO avanzado** | Sitemap, meta tags dinámicos, schema.org para LocalBusiness |
| **Multi-idioma** | Español (principal) + posible mapudungún/aymara para inclusión |

---

### Fase 4 — App Nativa (PWA / React Native)
> **Estado: ⏳ PENDIENTE**

| Opción | Ventajas | Desventajas |
|---|---|---|
| **PWA** | Sin app store, se instala desde el browser, mismo código | Menos acceso a APIs nativas |
| **React Native / Expo** | App nativa real, mejor rendimiento, push notifications | Código separado, más mantenimiento |

**Decisión recomendada**: Empezar con **PWA** (Progressive Web App) en Fase 4.1, luego migrar a **React Native** si el proyecto crece y necesita presencia en App Store/Play Store.

**Login obligatorio en app**: Cuando se detecte que la app está instalada (PWA o nativa), forzar login. En browser web normal, solo visualización pública.

---

## 📏 Reglas y Convenciones del Agente

### Código
- **Idioma del código**: Inglés (variables, funciones, comentarios técnicos)
- **Idioma de UI/UX**: Español (textos visibles al usuario)
- **Idioma de documentación**: Español
- **TypeScript**: Siempre con tipos explícitos, nunca `any`
- **Funciones**: Export con nombre, nunca default export (excepto páginas Next.js)
- **Errores**: Siempre try/catch con tipos de error definidos
- **Supabase**: Usar el query builder, nunca SQL raw en el frontend
- **Archivos**: kebab-case para archivos, PascalCase para componentes React

### Git
- **Commits**: Conventional Commits (`feat:`, `fix:`, `docs:`, `chore:`, `refactor:`)
- **Branches**: `feature/`, `fix/`, `docs/` + descripción corta
- **PRs**: Siempre referenciar issues con `Closes #N`
- **Reviews**: Todo PR requiere revisión antes de merge

### Supabase
- **RLS**: SIEMPRE habilitado en todas las tablas
- **UUID**: Siempre `gen_random_uuid()` como default para PKs
- **Timestamps**: Siempre `now()` como default para `created_at`
- **Storage**: Buckets separados por tipo (profile-photos, work-photos)
- **Políticas**: Mínimo privilegio — solo lo necesario

### Testing (futuro)
- Unit tests con Vitest para funciones de lib/
- E2E tests con Playwright para flujos críticos
- Mínimo 80% coverage en funciones de negocio

---

## 📁 Estructura del Proyecto

```
contruapp/
├── docs/
│   ├── MER.md                          # Modelo entidad-relación
│   ├── PROFESIONES_CHILE.md            # Catálogo de oficios
│   └── PLAN_ANTIGRAVITY.md             # Este archivo
├── supabase/
│   └── migrations/
│       ├── 001_create_tables.sql       # Creación de tablas + RLS
│       ├── 002_seed_professions.sql    # Seed de profesiones
│       └── 003_seed_regions_comunas.sql # Seed de regiones y comunas
├── src/
│   ├── app/                            # Next.js App Router (Fase 2)
│   │   ├── layout.tsx                  # Layout principal
│   │   ├── page.tsx                    # Home — grid de trabajadores
│   │   ├── login/
│   │   │   └── page.tsx               # Login
│   │   ├── register/
│   │   │   └── page.tsx               # Registro
│   │   ├── dashboard/
│   │   │   └── page.tsx               # Panel del trabajador
│   │   └── worker/
│   │       └── [id]/
│   │           └── page.tsx           # Perfil público
│   ├── components/                     # Componentes reutilizables (Fase 2)
│   │   ├── WorkerCard.tsx
│   │   ├── SearchBar.tsx
│   │   ├── FilterPanel.tsx
│   │   ├── LocationPermission.tsx
│   │   ├── Header.tsx
│   │   └── Footer.tsx
│   ├── lib/                            # Lógica de negocio (Fase 1)
│   │   ├── supabase.ts                # Cliente Supabase
│   │   ├── auth.ts                    # Autenticación
│   │   ├── workers.ts                 # CRUD de trabajadores
│   │   └── filters.ts                # Catálogos y filtros
│   └── types/
│       └── index.ts                   # Interfaces TypeScript
├── public/                             # Assets estáticos
│   └── images/
├── .env.example                        # Variables de entorno ejemplo
├── .gitignore
├── next.config.js
├── package.json
├── postcss.config.js
├── tailwind.config.ts
├── tsconfig.json
└── README.md
```

---

## ⚙️ Instrucciones de Setup

### 1. Clonar el repositorio
```bash
git clone https://github.com/NikoRNJ/contruapp.git
cd contruapp
npm install
```

### 2. Configurar Supabase
1. Crear un proyecto en [supabase.com](https://supabase.com)
2. Copiar la **Project URL** y la **anon public key**
3. Crear archivo `.env.local`:
```bash
cp .env.example .env.local
# Editar con tus credenciales de Supabase
```

### 3. Ejecutar migraciones SQL
En el **SQL Editor** de Supabase, ejecutar en orden:
1. `supabase/migrations/001_create_tables.sql`
2. `supabase/migrations/002_seed_professions.sql`
3. `supabase/migrations/003_seed_regions_comunas.sql`

### 4. Crear buckets de Storage
En Supabase Dashboard → Storage:
1. Crear bucket `profile-photos` (público)
2. Crear bucket `work-photos` (público)

### 5. Ejecutar el proyecto
```bash
npm run dev
# Abrir http://localhost:3000
```

---

## ✅ Checklist General

### Fase 1 — MVP Backend
- [ ] Modelo entidad-relación documentado (`docs/MER.md`)
- [ ] Scripts SQL de tablas con RLS (`supabase/migrations/001_create_tables.sql`)
- [ ] Seed de 22+ profesiones chilenas (`supabase/migrations/002_seed_professions.sql`)
- [ ] Seed de 16 regiones + 80+ comunas (`supabase/migrations/003_seed_regions_comunas.sql`)
- [ ] Documentación de profesiones (`docs/PROFESIONES_CHILE.md`)
- [ ] Cliente Supabase configurado (`src/lib/supabase.ts`)
- [ ] Auth: signup, login, logout, reset (`src/lib/auth.ts`)
- [ ] CRUD workers con filtros (`src/lib/workers.ts`)
- [ ] Catálogos de filtros (`src/lib/filters.ts`)
- [ ] Tipos TypeScript completos (`src/types/index.ts`)
- [ ] Package.json con dependencias correctas
- [ ] README profesional con instrucciones

### Fase 2 — Frontend
- [ ] Layout principal con Header/Footer
- [ ] Página Home con grid de cards (3 columnas)
- [ ] Componente WorkerCard
- [ ] Buscador superior (SearchBar)
- [ ] Panel de filtros (FilterPanel)
- [ ] Página de perfil de trabajador
- [ ] Página de login
- [ ] Página de registro
- [ ] Dashboard del trabajador
- [ ] Solicitud de permisos de ubicación
- [ ] Responsive: móvil, tablet, desktop

### Fase 3 — Avanzado
- [ ] Sistema de reseñas
- [ ] Verificación de perfiles
- [ ] Notificaciones
- [ ] Chat interno
- [ ] Favoritos
- [ ] SEO avanzado

### Fase 4 — App
- [ ] PWA manifest + service worker
- [ ] Detección de app instalada → forzar login
- [ ] Push notifications
- [ ] Evaluación React Native

---

## 🏆 Profesiones de Construcción en Chile (Referencia Rápida)

| Categoría | Oficios |
|---|---|
| **Jerarquías** | Jornal, Maestro de Segunda (M2), Maestro de Primera (M1), Capataz, Supervisor de Obra |
| **Obra Gruesa** | Albañil, Carpintero de Obra Gruesa, Enfierrador/Armador, Hormigonero/Concretero |
| **Instalaciones** | Electricista, Gasfíter/Plomero, Instalador Sanitario, Instalador de Gas |
| **Terminaciones** | Pintor, Yesero/Estucador, Ceramista, Instalador de Pisos, Montajista |
| **Especialistas** | Soldador, Techador, Instalador Drywall/Volcanita, Aislador Térmico/Acústico |
| **Maquinaria** | Gruero Torre, Operador Retroexcavadora, Operador Minicargador |

---

## 🇨🇱 Regiones de Chile (16)

| # | Región | Numeral | Capital |
|---|---|---|---|
| 1 | Arica y Parinacota | XV | Arica |
| 2 | Tarapacá | I | Iquique |
| 3 | Antofagasta | II | Antofagasta |
| 4 | Atacama | III | Copiapó |
| 5 | Coquimbo | IV | La Serena |
| 6 | Valparaíso | V | Valparaíso |
| 7 | Metropolitana de Santiago | RM | Santiago |
| 8 | O'Higgins | VI | Rancagua |
| 9 | Maule | VII | Talca |
| 10 | Ñuble | XVI | Chillán |
| 11 | Biobío | VIII | Concepción |
| 12 | La Araucanía | IX | Temuco |
| 13 | Los Ríos | XIV | Valdivia |
| 14 | Los Lagos | X | Puerto Montt |
| 15 | Aysén | XI | Coyhaique |
| 16 | Magallanes | XII | Punta Arenas |

---

> **Nota para el agente**: Este documento es tu guía maestra. Cada PR que crees debe referenciar las fases y skills aquí descritos. Ante cualquier duda de arquitectura o diseño, consulta este archivo primero. El objetivo es llevar Contruapp a la cima. 🚀👷‍♂️