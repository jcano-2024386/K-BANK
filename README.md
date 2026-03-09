# 🏦 K-Bank


## 📋 Tabla de Contenidos

- [Descripción](#-descripción)
- [Arquitectura](#-arquitectura)
- [Tecnologías](#-tecnologías)
- [Estructura del Proyecto](#-estructura-del-proyecto)
- [Metodología SCRUM](#-metodología-scrum)
- [Requisitos Previos](#-requisitos-previos)
- [Instalación y Configuración](#-instalación-y-configuración)
- [Variables de Entorno](#-variables-de-entorno)
- [Endpoints de la API](#-endpoints-de-la-api)
- [Modelos de Datos](#-modelos-de-datos)
- [Roles del Sistema](#-roles-del-sistema)
- [Seguridad](#-seguridad)
- [Administrador por Defecto](#-administrador-por-defecto)
- [Equipo](#-equipo)

---

## 📌 Descripción

**K-Bank** es un sistema bancario digital desarrollado como proyecto de Práctica Supervisada en el **Centro Educativo Técnico Laboral Kinal**. Permite gestionar de manera centralizada y segura las operaciones financieras esenciales de una entidad bancaria moderna:

- **Registro y autenticación** de usuarios con JWT, verificación de email y recuperación de contraseña
- **Cuentas bancarias** de tipo ahorro o monetaria, vinculadas al usuario propietario
- **Transacciones atómicas** de depósito, retiro y transferencia con validación de saldo en tiempo real
- **Límites de seguridad** por transferencia (Q2,000) y límite diario acumulado (Q10,000)
- **Reversión de depósitos** dentro de los primeros 60 segundos de realizados
- **Historial de movimientos** completo con trazabilidad de cuentas origen y destino
- **Préstamos bancarios** con cálculo de cuota mensual, plazo e interés
- **Control de roles**: ADMIN y CLIENTE con accesos diferenciados
- **Historial de inicio de sesión** por dirección IP registrada por el Auth Service
- Microservicio de **autenticación dedicado** en ASP.NET Core 8 con Clean Architecture

---

## 🏗️ Arquitectura

K-Bank utiliza una **arquitectura de microservicios** con responsabilidades claramente separadas:

```
K-Bank/
├── 🔐 Auth Service (ASP.NET Core 8)
│   ├── Registro con foto de perfil (Cloudinary)
│   ├── Login con email o username
│   ├── Verificación de email con token (24h)
│   ├── Recuperación y reseteo de contraseña (1h)
│   ├── Generación y validación de JWT
│   ├── Gestión de roles
│   └── Historial de inicio de sesión por IP y país
│
├── 🏦 Banking API (Node.js + Express)
│   ├── Registro y login de usuarios bancarios
│   ├── Gestión de cuentas (ahorro / monetaria)
│   ├── Depósitos, retiros y transferencias (atómicas)
│   ├── Historial de transacciones
│   ├── Préstamos bancarios
│   └── Empleados y Sucursales
│
└── 🖥️ Frontend (React + Vite) [pendiente de integración]
```

El **Auth Service** implementa **Clean Architecture** en 4 capas desacopladas:

| Capa | Proyecto | Responsabilidad |
|---|---|---|
| API / Presentación | `AuthServiceBanco.Api` | Controladores, middlewares, configuración HTTP |
| Aplicación | `AuthServiceBanco.Application` | Servicios, DTOs, interfaces, validadores |
| Dominio | `AuthServiceBanco.Domain` | Entidades, enums, constantes de roles |
| Persistencia | `AuthServiceBanco.Persistence` | DbContext, repositorios, seeder de datos |

---

## 🛠️ Tecnologías

### Banking API — Node.js

| Tecnología | Versión | Uso |
|---|---|---|
| Node.js | ≥ 18 | Runtime de JavaScript |
| Express | ^5.2 | Framework HTTP |
| MongoDB + Mongoose | ^9.2 | Base de datos NoSQL |
| JWT (jsonwebtoken) | ^9.0 | Autenticación con tokens |
| bcryptjs | ^2.4 | Hash seguro de contraseñas |
| Helmet | ^8.1 | Protección de cabeceras HTTP |
| express-rate-limit | ^8.2 | Limitación de solicitudes por IP |
| express-validator | ^7.3 | Validación estricta de campos |
| Morgan | ^1.10 | Logging de peticiones HTTP |
| Cloudinary SDK | ^2.9 | Almacenamiento de imágenes |
| Multer | ^2.0 | Manejo de archivos multipart |
| UUID | ^13.0 | Generación de identificadores únicos |

### Auth Service — ASP.NET Core

| Tecnología | Uso |
|---|---|
| ASP.NET Core 8 | Framework web de alto rendimiento |
| PostgreSQL 16 | Base de datos relacional |
| Entity Framework Core | ORM para acceso a datos |
| JWT Bearer | Autenticación con tokens firmados |
| Swagger / OpenAPI | Documentación interactiva de la API |
| Cloudinary SDK | Carga y gestión de fotos de perfil |
| SMTP / Email Service | Verificación de email y recuperación de contraseña |

### Frontend

| Tecnología | Uso |
|---|---|
| React | Biblioteca de interfaz de usuario |
| Vite | Bundler y servidor de desarrollo |
| pnpm | Gestor de paquetes |

### DevOps y Herramientas

| Herramienta | Uso |
|---|---|
| Docker | Contenedor para PostgreSQL del Auth Service |
| GitHub | Control de versiones |
| Postman | Pruebas de la API |
| Notion | Gestión de tareas y backlog |
| Visual Studio Code | Entorno de desarrollo principal |

---

## 📁 Estructura del Proyecto

```
K BANK/
├── configs/
│   ├── app.js                          # Configuración de Express y rutas
│   ├── db.js                           # Conexión a MongoDB con eventos de estado
│   ├── cors-configuration.js           # Configuración de CORS
│   └── helmet-configuration.js         # Configuración de seguridad HTTP
│
├── middlewares/
│   ├── validate-JWT.js                 # Validación y decodificación de JWT
│   ├── validate-role.js                # Control de acceso por rol
│   ├── auth-validators.js              # Validación de campos de registro/login
│   ├── field-validators.js             # Validadores genéricos de campos
│   ├── checkValidators.js              # Manejo de errores de validación
│   ├── handle-errors.js                # Middleware global de errores
│   ├── request-limit.js                # Rate limiting (100 req / 15 min)
│   ├── file-uploader.js                # Carga de archivos con Multer
│   └── delete-file-on-error.js         # Limpieza de archivos en errores
│
├── helpers/
│   └── generate-jwt.js                 # Generador de JWT con claims personalizados
│
├── utils/
│   └── seed-admin.js                   # Creación automática del administrador
│
├── src/
│   ├── controllers/
│   │   ├── user.controller.js          # Registro, login, CRUD de usuarios
│   │   ├── transacciones.controller.js # Depósitos, retiros, transferencias
│   │   ├── divisas.controller.js       # (pendiente)
│   │   ├── empleado.controller.js      # Gestión de empleados
│   │   ├── prestamo.controller.js      # Préstamos bancarios
│   │   ├── sucursal.controller.js      # Gestión de sucursales
│   │   └── promociones.controller.js   # Promociones y ofertas
│   │
│   ├── models/
│   │   ├── user.model.js               # Esquema de usuario con DPI inmutable
│   │   ├── cuenta.model.js             # Cuentas bancarias
│   │   ├── transacciones.model.js      # Historial de transacciones
│   │   ├── prestamo.model.js           # Préstamos con cuota mensual
│   │   ├── divisas.model.js            # (pendiente)
│   │   ├── empleado.model.js           # Empleados
│   │   ├── sucursal.model.js           # Sucursales
│   │   └── promociones.model.js        # Promociones
│   │
│   ├── routes/
│   │   ├── user.routes.js              # Rutas de autenticación
│   │   ├── transacciones.routes.js     # Rutas de transacciones
│   │   ├── cuenta.routes.js            # Rutas de cuentas
│   │   ├── divisa.routes.js            # (pendiente)
│   │   ├── prestamo.routes.js          # Rutas de préstamos
│   │   ├── empleado.routes.js          # Rutas de empleados
│   │   ├── sucursal.routes.js          # Rutas de sucursales
│   │   └── promociones.routes.js       # Rutas de promociones
│   │
│   └── AuthServiceBanco.*              # Microservicio .NET — Clean Architecture (4 proyectos)
│
├── frontend/                           # Aplicación React + Vite
├── index.js                            # Entry point del servidor
├── docker-compose.yml                  # PostgreSQL para el Auth Service
└── .env                                # Variables de entorno
```

---

## 🔄 Metodología SCRUM

El proyecto se desarrolla en **4 sprints de 2 semanas** (duración total: 8 semanas):

| Sprint | Semanas | Objetivo | Estado |
|---|---|---|---|
| **Sprint 1** | 1 – 2 | Diseño de base de datos, configuración del entorno, autenticación con JWT, registro y login | ✅ Completo |
| **Sprint 2** | 3 – 4 | Gestión de cuentas bancarias: crear cuenta, consultar saldo, depósitos y retiros | ✅ Completo |
| **Sprint 3** | 5 – 6 | Transferencias entre cuentas, historial de transacciones, validaciones de seguridad | ✅ Completo |
| **Sprint 4** | 7 – 8 | Conversión de divisas, pruebas finales, corrección de errores y documentación | 🔄 En progreso |

### Roles SCRUM

| Rol | Integrante | Responsabilidades |
|---|---|---|
| **Scrum Master** | Jeferson Cano | Elimina impedimentos, facilita procesos, coordina reuniones, asegura el cumplimiento de la metodología |
| **Product Owner** | Otto Díaz | Define y prioriza el Product Backlog, valida que el producto cumpla los objetivos, comunicación con stakeholders |

### Medición del Progreso

El avance se mide mediante:
- Número de tareas completadas por sprint
- Comparación entre tareas pendientes y terminadas
- Revisión semanal del backlog

---

## ✅ Requisitos Previos

- **Node.js** ≥ 18 y **pnpm** ≥ 10
- **.NET SDK** 8.0 (para el Auth Service)
- **MongoDB** (local o MongoDB Atlas)
- **Docker** (para PostgreSQL del Auth Service)
- Cuenta en **Cloudinary** (fotos de perfil de usuarios)
- Cuenta en un proveedor **SMTP** (para emails de verificación y recuperación)

---

## 🚀 Instalación y Configuración

### 1. Clonar el repositorio

```bash
git clone https://github.com/tu-usuario/K-BANK.git
cd "K BANK"
```

### 2. Instalar dependencias del Backend (Node.js)

```bash
pnpm install
```

### 3. Configurar variables de entorno

```bash
cp .env.example .env
# Editar .env con tus credenciales
```

### 4. Levantar PostgreSQL con Docker (para Auth Service)

```bash
docker-compose up -d
```

### 5. Iniciar el servidor bancario en desarrollo

```bash
pnpm dev
```

### 6. Instalar y correr el frontend (en otra terminal)

```bash
cd frontend
pnpm install
pnpm dev
```

> ⚠️ **Nunca subas tu `.env` real al repositorio.** Agrégalo al `.gitignore`.

---

Para el Auth Service (.NET), configura `appsettings.json` con la cadena de conexión a PostgreSQL, los parámetros JWT y las credenciales SMTP.

---

## 🔌 Endpoints de la API

Base URL: `/SistemaBancarioAdmin/v1`

Los endpoints protegidos requieren: `Authorization: Bearer <token>` o `x-token: <token>`

---

### 🔐 Autenticación — `/auth`

| Método | Endpoint | Descripción | Protegido |
|---|---|---|---|
| `POST` | `/auth/register` | Registrar nuevo usuario bancario | No |
| `POST` | `/auth/login` | Iniciar sesión, retorna JWT | No |
| `GET` | `/auth/` | Listar todos los usuarios activos | ✅ ADMIN |
| `PUT` | `/auth/:id` | Actualizar datos de usuario | ✅ JWT |
| `DELETE` | `/auth/:id` | Desactivar usuario (soft delete) | ✅ ADMIN |

---

### 🏦 Auth Service — `api/v1/Auth` (ASP.NET Core)

| Método | Endpoint | Descripción | Protegido |
|---|---|---|---|
| `POST` | `/Auth/register` | Registro con foto de perfil | No |
| `POST` | `/Auth/login` | Login por email o username | No |
| `GET` | `/Auth/profile` | Ver perfil del usuario autenticado | ✅ JWT |
| `POST` | `/Auth/verify-email` | Verificar email con token (24h) | No |
| `POST` | `/Auth/resend-verification` | Reenviar email de verificación | No |
| `POST` | `/Auth/forgot-password` | Solicitar recuperación de contraseña | No |
| `POST` | `/Auth/reset-password` | Resetear contraseña con token (1h) | No |
| `GET` | `/Auth/login-history` | Ver historial de inicios de sesión | ✅ JWT |

---

### 💳 Cuentas — `/cuentas`

| Método | Endpoint | Descripción | Protegido |
|---|---|---|---|
| `POST` | `/cuentas/` | Crear nueva cuenta bancaria | ✅ JWT |
| `GET` | `/cuentas/` | Listar todas las cuentas | ✅ JWT |
| `GET` | `/cuentas/:id` | Obtener cuenta por ID | ✅ JWT |
| `PUT` | `/cuentas/:id` | Actualizar cuenta | ✅ JWT |
| `DELETE` | `/cuentas/:id` | Eliminar cuenta | ✅ JWT |

**Tipos de cuenta:** `ahorro` · `monetaria`

---

### 💸 Transacciones — `/transacciones`

| Método | Endpoint | Descripción | Protegido |
|---|---|---|---|
| `POST` | `/transacciones/` | Crear transacción (depósito, retiro o transferencia) | ✅ JWT |
| `GET` | `/transacciones/` | Obtener historial completo de transacciones | ✅ JWT |
| `GET` | `/transacciones/:id` | Obtener transacción por ID | ✅ JWT |
| `PUT` | `/transacciones/:id` | Modificar o revertir un depósito | ✅ JWT |

**Tipos de transacción:** `DEPOSITO` · `RETIRO` · `TRANSFERENCIA`

**Reglas de negocio:**
- Monto máximo por transferencia individual: **Q2,000**
- Límite diario acumulado de transferencias: **Q10,000**
- Reversión de depósitos disponible hasta **60 segundos** después de realizados
- Solo el propietario o un ADMIN puede modificar o revertir un depósito
- Las transferencias ejecutan operaciones atómicas con sesión de Mongoose para garantizar consistencia

---

### 💰 Préstamos — `/prestamos`

| Método | Endpoint | Descripción | Protegido |
|---|---|---|---|
| `POST` | `/prestamos/` | Crear préstamo bancario | ✅ JWT |
| `GET` | `/prestamos/` | Listar préstamos | ✅ JWT |
| `GET` | `/prestamos/:id` | Obtener préstamo por ID | ✅ JWT |
| `PUT` | `/prestamos/:id` | Actualizar préstamo | ✅ JWT |
| `DELETE` | `/prestamos/:id` | Eliminar préstamo | ✅ ADMIN |

---

### 🌍 Divisas — `/divisas`

| Método | Endpoint | Descripción | Protegido |
|---|---|---|---|
| — | `/divisas/` | _Módulo pendiente de implementar_ | — |

---

### 👷 Empleados — `/empleados`

| Método | Endpoint | Descripción | Protegido |
|---|---|---|---|
| `POST` | `/empleados/` | Registrar empleado | ✅ ADMIN |
| `GET` | `/empleados/` | Listar empleados | ✅ JWT |
| `GET` | `/empleados/:id` | Obtener empleado por ID | ✅ JWT |
| `PUT` | `/empleados/:id` | Actualizar empleado | ✅ ADMIN |
| `DELETE` | `/empleados/:id` | Eliminar empleado | ✅ ADMIN |

---

### 🏢 Sucursales — `/sucursales`

| Método | Endpoint | Descripción | Protegido |
|---|---|---|---|
| `POST` | `/sucursales/` | Crear sucursal | ✅ ADMIN |
| `GET` | `/sucursales/` | Listar sucursales | ✅ JWT |
| `GET` | `/sucursales/:id` | Obtener sucursal por ID | ✅ JWT |
| `PUT` | `/sucursales/:id` | Actualizar sucursal | ✅ ADMIN |
| `DELETE` | `/sucursales/:id` | Eliminar sucursal | ✅ ADMIN |

---

### ❤️ Health Check

```
GET /SistemaBancarioAdmin/v1/Health
```

Retorna el estado del servidor, timestamp y nombre del servicio.

---

## 🗄️ Modelos de Datos

### Usuario (`User`)

| Campo | Tipo | Descripción |
|---|---|---|
| `numeroCuenta` | String | Número de cuenta de 10 dígitos, generado automáticamente |
| `name` | String | Nombre completo del usuario |
| `username` | String | Nombre de usuario único |
| `dpi` | String | Documento de identidad (inmutable tras creación) |
| `email` | String | Correo electrónico único (minúsculas) |
| `password` | String | Contraseña hasheada con bcrypt (salt: 10) |
| `address` | String | Dirección de residencia |
| `cellphone` | String | Teléfono de 8 dígitos |
| `workPlace` | String | Lugar de trabajo |
| `monthlyIncome` | Number | Ingresos mensuales (mínimo Q100) |
| `balance` | Number | Saldo general (mínimo 0) |
| `rol` | Enum | `ADMIN` / `CLIENTE` |
| `estado` | Boolean | Estado activo / desactivado (soft delete) |

> 🔒 El campo `dpi` es **inmutable**: el modelo previene su modificación mediante un hook `pre('findOneAndUpdate')`.

---

### Cuenta (`Cuenta`)

| Campo | Tipo | Descripción |
|---|---|---|
| `numeroCuenta` | String | Número único de la cuenta |
| `saldo` | Number | Saldo actual (default: 0) |
| `tipo` | Enum | `ahorro` / `monetaria` |
| `usuario` | ObjectId | Referencia al usuario propietario |

---

### Transacción (`Transaccion`)

| Campo | Tipo | Descripción |
|---|---|---|
| `tipo` | Enum | `DEPOSITO` / `RETIRO` / `TRANSFERENCIA` |
| `monto` | Number | Monto de la operación (mayor a 0) |
| `descripcion` | String | Descripción opcional de la transacción |
| `usuario` | ObjectId | Usuario que realizó la transacción |
| `cuentaOrigen` | ObjectId | Cuenta de donde sale el dinero |
| `cuentaDestino` | ObjectId | Cuenta de destino (solo en transferencias) |
| `estado` | Boolean | `true` = activa, `false` = revertida |

---

### Préstamo (`Prestamo`)

| Campo | Tipo | Descripción |
|---|---|---|
| `monto` | Number | Monto total del préstamo |
| `interes` | Number | Tasa de interés (%) |
| `plazo` | Number | Plazo en meses |
| `cuotaMensual` | Number | Cuota mensual calculada |
| `estado` | Boolean | Estado activo del préstamo |
| `usuario` | ObjectId | Usuario beneficiario |
| `cuenta` | ObjectId | Cuenta asociada al préstamo |

---

## 👥 Roles del Sistema

| Rol | Descripción | Acceso |
|---|---|---|
| `ADMIN` | Administrador del sistema | Acceso total — usuarios, cuentas, transacciones, empleados, sucursales |
| `CLIENTE` | Usuario bancario estándar | Consulta de saldo, depósitos, retiros, transferencias e historial propio |

---

## 🔒 Seguridad

- Contraseñas hasheadas con **bcryptjs** (salt rounds: 10)
- Autenticación mediante **JWT** con expiración configurable (default: 30 min)
- JWT incluye claims: `sub` (userId), `jti` (ID único del token), `iat`, `role`, `issuer`, `audience`
- Token aceptado vía header `Authorization: Bearer <token>` o `x-token: <token>`
- **Verificación de email** obligatoria en el Auth Service antes del primer acceso
- **Recuperación de contraseña** con token de expiración de 1 hora
- Campo **DPI inmutable** a nivel de modelo para proteger datos de identidad
- Cabeceras HTTP protegidas con **Helmet**
- **Rate Limiting** activo: 100 solicitudes por IP cada 15 minutos
- Validación estricta de campos con **express-validator**
- **CORS** configurado explícitamente (métodos: GET, POST, PUT, DELETE, OPTIONS)
- Manejo global de excepciones con middleware dedicado
- **Historial de login** con registro de IP y país por sesión (Auth Service)
- Conexión a MongoDB con pool de 10 conexiones y timeout de 5 segundos
- Cierre controlado de la base de datos ante señales `SIGINT`, `SIGTERM` y `SIGUSR2`
- La contraseña nunca se retorna en respuestas gracias al método `toJSON` del modelo

---

## 🔑 Administrador por Defecto

Al iniciar el servidor por primera vez, el sistema crea automáticamente un usuario administrador:

| Campo | Valor |
|---|---|
| Username | `ADMINB` |
| Email | `admin@banco.com` |
| Password | `ADMINB` |
| Rol | `ADMIN` |

> ⚠️ Se recomienda cambiar la contraseña del administrador inmediatamente después del primer inicio.

---

## 👥 Equipo

| Integrante | Carné | Rol SCRUM | Área | Responsabilidades |
|---|---|---|---|---|
| **Jeferson André Cano López** | 2024386 | Scrum Master | Backend & Base de Datos | Desarrollo de endpoints bancarios, modelado y consultas de base de datos, lógica de transacciones atómicas, Auth Service |
| **Otto Raúl Díaz Batres** | 2024248 | Product Owner | Frontend & Integración | Definición de requerimientos, autenticación y validaciones, diseño de UI en React, integración general |

---

## 📄 Información Académica

| Campo | Detalle |
|---|---|
| **Institución** | Centro Educativo Técnico Laboral Kinal |
| **Curso** | Práctica Supervisada |
| **Profesor** | Braulio Echeverría |
| **Fecha de Planificación** | 1 de marzo de 2026 |

---
