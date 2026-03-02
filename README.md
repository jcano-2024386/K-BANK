#  K-BANK — Sistema de Banco

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

Los endpoints protegidos requieren: `Authorization: Bearer <token>` o `x-token: <token>`

| Módulo | Ruta Base | Operaciones |
|---|---|---|
| Auth | `/api/auth` | register, login, getUsers, update, delete |
| Cuentas | `/api/cuentas` | Crear cuenta, consultar saldo, listar |
| Transacciones | `/api/transacciones` | Depósito, retiro, transferencia, historial, modificar/revertir depósito |
| Divisas | `/api/divisas` | Conversión entre monedas, consulta de tasas, historial de conversiones |
| Empleados | `/api/empleados` | CRUD completo |
| Sucursales | `/api/sucursales` | CRUD completo |
| Préstamos | `/api/prestamos` | CRUD completo |
| Promociones | `/api/promociones` | CRUD completo |

## 👤 Roles

| Rol | Acceso |
|---|---|
| `ADMIN` | Total — gestión de usuarios, cuentas, transacciones y configuración |
| `CLIENTE` | Consulta de saldo, depósitos, retiros, transferencias e historial propio |

---

## 🏗️ Arquitectura

El sistema se divide en **3 servicios independientes**:

**1. Servicio de Autenticación**
- Registro y login de usuarios
- Generación y validación de JWT
- Gestión de roles (ADMIN / CLIENTE)

**2. Servicio Bancario**
- Creación de cuentas (ahorro / monetaria)
- Consulta de saldo
- Depósitos, retiros y transferencias con transacciones atómicas
- Historial de movimientos

**3. Servicio de Divisas**
- Conversión entre monedas
- Consulta de tasas de cambio
- Registro de conversiones realizadas

## 🔧 Herramientas

`Notion` `GitHub` `WhatsApp` `Visual Studio Code` `Postman`
---

*Centro Educativo Técnico Laboral Kinal · Guatemala 2026*
