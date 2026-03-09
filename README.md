# 🏦 K-Bank — Sistema Bancario Digital

**Kinal | Práctica Supervisada 2026 | Profesor: Braulio Echeverría**

| Integrante | Carné | Rol |
|---|---|---|
| Jeferson André Cano López | 2024386 | Scrum Master / Backend & Base de Datos |
| Otto Raúl Díaz Batres | 2024248 | Product Owner / Frontend & Integración |

---

## 🛠️ Tecnologías

`Node.js` `Express.js` `MongoDB` `Mongoose` `JWT` `bcryptjs` `React` `Vite` `pnpm`

---

## 🔌 Endpoints

Base URL: `/SistemaBancarioAdmin/v1`

Los endpoints protegidos requieren: `Authorization: Bearer <token>`

| Módulo | Ruta Base | Método | Operación | Protegido |
|---|---|---|---|---|
| Auth | `/auth/register` | POST | Registro de usuario | No |
| Auth | `/auth/login` | POST | Inicio de sesión | No |
| Auth | `/auth/` | GET | Listar usuarios | ✅ ADMIN |
| Auth | `/auth/:id` | PUT | Actualizar usuario | ✅ JWT |
| Auth | `/auth/:id` | DELETE | Eliminar usuario | ✅ ADMIN |
| Cuentas | `/cuentas/` | POST | Crear cuenta | ✅ JWT |
| Cuentas | `/cuentas/` | GET | Listar cuentas | ✅ JWT |
| Cuentas | `/cuentas/:id` | GET | Obtener cuenta | ✅ JWT |
| Cuentas | `/cuentas/:id` | PUT | Actualizar cuenta | ✅ JWT |
| Cuentas | `/cuentas/:id` | DELETE | Eliminar cuenta | ✅ JWT |
| Transacciones | `/transacciones/` | POST | Crear transacción | ✅ JWT |
| Transacciones | `/transacciones/` | GET | Historial completo | ✅ JWT |
| Transacciones | `/transacciones/:id` | GET | Obtener transacción | ✅ JWT |
| Transacciones | `/transacciones/:id` | PUT | Modificar / revertir depósito | ✅ JWT |
| Divisas | `/divisas/` | — | _Pendiente de implementar_ | — |

**Tipos de transacción:** `DEPOSITO` · `RETIRO` · `TRANSFERENCIA`

> 💡 Límite por transferencia: **Q2,000** · Límite diario acumulado: **Q10,000**

---

## 👤 Roles

| Rol | Acceso |
|---|---|
| `ADMIN` | Acceso total — gestión de usuarios, cuentas y transacciones |
| `CLIENTE` | Consulta de saldo, depósitos, retiros, transferencias e historial propio |

---

## 🏗️ Arquitectura

El sistema se divide en **3 servicios independientes:**

**1. Servicio de Autenticación**
- Registro y login de usuarios
- Generación y validación de JWT
- Gestión de roles (ADMIN / CLIENTE)
- Desactivación de cuentas (soft delete)

**2. Servicio Bancario**
- Creación de cuentas (`ahorro` / `monetaria`)
- Consulta de saldo
- Depósitos, retiros y transferencias con transacciones atómicas
- Historial de movimientos con populate de cuentas y usuarios
- Reversión de depósitos dentro de 1 minuto

**3. Servicio de Divisas** _(en desarrollo)_
- Conversión entre monedas
- Consulta de tasas de cambio
- Registro de conversiones realizadas

---

## 📅 Sprints SCRUM

| Sprint | Semanas | Objetivo | Estado |
|---|---|---|---|
| 1 | 1–2 | Diseño de BD, configuración del entorno, autenticación con JWT, registro y login | ✅ Completo |
| 2 | 3–4 | Gestión de cuentas bancarias, consultar saldo, depósitos y retiros | ✅ Completo |
| 3 | 5–6 | Transferencias entre cuentas, historial de transacciones, validaciones de seguridad | ✅ Completo |
| 4 | 7–8 | Conversión de divisas, pruebas finales, corrección de errores y documentación | SIN COMPLETAR :( |

## 📁 Estructura del Proyecto

```
K BANK/
├── configs/          # Configuración de Express, DB, CORS, Helmet
├── middlewares/      # JWT, roles, validadores, rate limit
├── helpers/          # Generador de JWT
├── src/
│   ├── controllers/  # Lógica de negocio
│   ├── models/       # Esquemas de Mongoose
│   └── routes/       # Definición de endpoints
├── frontend/         # Aplicación React + Vite
├── utils/            # Seed de administrador inicial
└── index.js          # Punto de entrada
```

---
