# 🏥 Farmacia San Marcos — Sistema de Gestión con Microservicios

> Sistema distribuido de gestión farmacéutica que simula la cadena completa de suministro: **Laboratorio → Distribuidor → Farmacia → Cliente final → Reportes**.

![Java](https://img.shields.io/badge/Java-17-orange?style=flat-square&logo=openjdk)
![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.2.0-6DB33F?style=flat-square&logo=springboot)
![Spring Cloud](https://img.shields.io/badge/Spring_Cloud-2023.0.0-6DB33F?style=flat-square&logo=spring)
![MySQL](https://img.shields.io/badge/MySQL-8.0-4479A1?style=flat-square&logo=mysql)
![Maven](https://img.shields.io/badge/Maven-Multi--módulo-C71A36?style=flat-square&logo=apachemaven)
![License](https://img.shields.io/badge/License-Académico-blue?style=flat-square)

---

## 📋 Tabla de Contenido

- [Descripción](#-descripción)
- [Arquitectura](#-arquitectura)
- [Tecnologías](#%EF%B8%8F-tecnologías)
- [Microservicios](#-microservicios)
- [Estructura del Proyecto](#-estructura-del-proyecto)
- [Requisitos Previos](#-requisitos-previos)
- [Instalación y Ejecución](#-instalación-y-ejecución)
- [Endpoints Principales](#-endpoints-principales)
- [Credenciales](#-credenciales)
- [Frontend](#-frontend)
- [Flujo de Prueba End-to-End](#-flujo-de-prueba-end-to-end)
- [Características Destacadas](#-características-destacadas)
- [Mejoras Futuras](#-mejoras-futuras)
- [Autor](#-autor)

---

## 📖 Descripción

Sistema distribuido implementado con **arquitectura de microservicios** que modela el flujo completo de la cadena de suministro farmacéutico en el contexto peruano. El proyecto demuestra los principios fundamentales de los sistemas distribuidos modernos: descubrimiento de servicios, comunicación entre microservicios, múltiples esquemas de seguridad, persistencia independiente y exposición de APIs en distintos protocolos.

Cada microservicio es **autónomo**, tiene su propia base de datos, y se comunica con los demás mediante **OpenFeign** sobre el registro **Netflix Eureka**.

---

## 🏗️ Arquitectura

```
┌─────────────────────────────────────────────────────────────┐
│                  EUREKA SERVER (puerto 8000)                │
│                  Service Discovery & Registry                │
└─────────┬─────────────┬─────────────┬───────────────┬───────┘
          │             │             │               │
     ┌────┴─────┐ ┌─────┴─────┐ ┌────┴────┐ ┌────────┴──────┐
     │   LAB    │ │   DIST    │ │  FARM   │ │   REPORTES    │
     │  :8001   │→│   :8002   │→│  :8003  │→│    :8004      │
     │BasicAuth │ │    JWT    │ │ OAuth2  │ │  SOAP + REST  │
     │  MySQL   │ │   MySQL   │ │  MySQL  │ │     MySQL     │
     └──────────┘ └───────────┘ └─────────┘ └───────────────┘

   ─ ─ ─ Registro/Discovery     ─────  Comunicación OpenFeign
```

Cada microservicio tiene su **propia base de datos MySQL** (patrón _Database per Service_).

---

## 🛠️ Tecnologías

### Backend
- **Java 17**
- **Spring Boot 3.2.0**
- **Spring Cloud 2023.0.0** — Eureka, OpenFeign
- **Spring Security** — Basic Auth, JWT, OAuth2
- **Spring Data JPA + Hibernate**
- **Spring Web Services** — SOAP/WSDL
- **JJWT** — JSON Web Tokens
- **Maven** — Multi-módulo

### Base de Datos
- **MySQL 8** — 4 bases de datos independientes (Database per Service)

### Frontend
- **HTML5 + CSS3 + JavaScript (ES6+)**
- **Fetch API** — Consumo de microservicios
- **SPA** — Single Page Application

---

## 📦 Microservicios

| # | Microservicio | Puerto | Seguridad | Base de Datos | Responsabilidad |
|---|---|---|---|---|---|
| 1 | **eureka-server** | 8000 | — | — | Registro y descubrimiento de servicios |
| 2 | **ms-laboratorio** | 8001 | Basic Auth | `farmacia_laboratorio` | Producción de lotes de medicamentos |
| 3 | **ms-distribuidor** | 8002 | JWT (Bearer) | `farmacia_distribuidor` | Recepción y despacho de inventario |
| 4 | **ms-farmacia** | 8003 | OAuth2 Resource Server | `farmacia_farmacia` | Stock y venta al cliente final |
| 5 | **ms-reportes-soap** | 8004 | SOAP + REST | `farmacia_reportes` | Reportes y estadísticas consolidadas |

### Total
- **5 microservicios**
- **17 endpoints** (16 REST + 1 SOAP)
- **3 esquemas de seguridad**
- **4 bases de datos MySQL**

---

## 📁 Estructura del Proyecto

```
farmacia-microservicios/
├── sql/
│   ├── 01_crear_bases_datos.sql       # Crea las 4 BDs y el usuario
│   └── 02_tablas_y_datos.sql          # Tablas + datos iniciales (peruanos)
│
├── eureka-server/                      # Service Registry (sin BD)
├── ms-laboratorio/                     # MS Producción
├── ms-distribuidor/                    # MS Distribución (JWT)
├── ms-farmacia/                        # MS Venta al cliente
├── ms-reportes-soap/                   # MS Reportes (SOAP + REST)
│
└── README.md
```

---

## ✅ Requisitos Previos

- **Java JDK 17** ([Eclipse Temurin](https://adoptium.net/) recomendado)
- **Maven 3.8+**
- **MySQL 8** ([Descargar](https://dev.mysql.com/downloads/mysql/))
- **MySQL Workbench** (opcional, recomendado para gestionar la BD)
- **IntelliJ IDEA** o **Eclipse** (opcional pero recomendado)

---

## 🚀 Instalación y Ejecución

### 1️⃣ Clonar el repositorio

```bash
git clone https://github.com/TU_USUARIO/farmacia-microservicios.git
cd farmacia-microservicios
```

### 2️⃣ Crear las bases de datos

Ejecuta los scripts SQL en orden:

```bash
mysql -u root -p < sql/01_crear_bases_datos.sql
mysql -u root -p < sql/02_tablas_y_datos.sql
```

O bien, ábrelos en **MySQL Workbench** y ejecútalos uno por uno (`Ctrl + Shift + Enter`).

Esto creará 4 bases de datos:
- `farmacia_laboratorio`
- `farmacia_distribuidor`
- `farmacia_farmacia`
- `farmacia_reportes`

Y el usuario `farmacia_user` con contraseña `farmacia2025`.

### 3️⃣ Abrir el proyecto

En IntelliJ: `File → Open → ` selecciona la carpeta raíz `farmacia-microservicios`.

Espera a que Maven descargue todas las dependencias.

### 4️⃣ Levantar los microservicios

**⚠️ Importante: respetar el orden de arranque.**

| # | Microservicio | Clase Main |
|---|---|---|
| 1 | **eureka-server** | `EurekaServerApplication` |
| 2 | **ms-laboratorio** | `LaboratorioApplication` |
| 3 | **ms-distribuidor** | `DistribuidorApplication` |
| 4 | **ms-farmacia** | `FarmaciaApplication` |
| 5 | **ms-reportes-soap** | `ReportesSoapApplication` |

### 5️⃣ Verificar que todo arrancó

Abre el dashboard de Eureka:
👉 http://localhost:8000

Deberías ver los 4 microservicios registrados ✅

---

## 🌐 Endpoints Principales

### Públicos (sin autenticación)
- 📚 Catálogo: `GET http://localhost:8003/farmacia/public/catalogo`
- 📊 Resumen: `GET http://localhost:8004/reportes/resumen`
- 📄 Reporte XML: `GET http://localhost:8004/reportes/xml`
- 📋 WSDL SOAP: `GET http://localhost:8004/ws/reportes.wsdl`

### Con autenticación

#### 🟢 ms-laboratorio (Basic Auth)
```http
POST   /laboratorio/producir          # Crear nuevo lote
GET    /laboratorio/lotes             # Listar lotes producidos
POST   /laboratorio/enviar/{loteId}   # Enviar al distribuidor (Feign)
```

#### 🟡 ms-distribuidor (JWT)
```http
POST   /auth/login                          # Obtener token JWT
POST   /distribuidor/recibir                # Recibir lote del laboratorio
GET    /distribuidor/inventario             # Ver inventario
POST   /distribuidor/despachar/{loteId}     # Despachar a farmacia (Feign)
```

#### 🔴 ms-farmacia (OAuth2)
```http
POST   /farmacia/recibir                                     # Recibir del distribuidor
GET    /farmacia/stock                                       # Ver stock disponible
POST   /farmacia/vender/{loteId}?cantidad=X&cliente=Y        # Registrar venta
GET    /farmacia/ventas                                      # Historial de ventas
```

#### 🟣 ms-reportes-soap
```http
POST   /reportes/registrar-venta             # Registro de venta (Feign)
GET    /reportes/ventas                      # Listar ventas
GET    /reportes/resumen                     # Resumen agregado
GET    /reportes/xml                         # Reporte en XML
POST   /ws  (SOAP getReporteRequest)         # Operación SOAP
```

---

## 🔐 Credenciales

| Componente | Usuario | Contraseña |
|---|---|---|
| MySQL (BD) | `farmacia_user` | `farmacia2025` |
| ms-laboratorio (Basic Auth) | `laboratorio` | `farmacia2025` |
| ms-distribuidor (JWT login) | `distribuidor` | `farmacia2025` |

---

## 💻 Frontend

El proyecto incluye un frontend web minimalista que consume los 5 microservicios desde una interfaz visual con tema farmacéutico.

### Características
- **Login con JWT real** vía `/auth/login`
- **7 pestañas funcionales**: Dashboard, Catálogo, Laboratorio, Distribuidor, Farmacia, Ventas, Reportes
- **Maneja los 3 esquemas de autenticación** (Basic Auth, Bearer, público)
- **Acceso al WSDL** y al reporte XML del servicio SOAP
- **Notificaciones tipo toast** y modales para formularios
- **Construido con HTML + CSS + JS puro** (sin frameworks)

### Cómo usar el frontend

> Requiere habilitar **CORS** en los microservicios — ver instrucciones en la carpeta del frontend.

1. Configurar CORS en los 4 microservicios (lab, dist, farm, reportes).
2. Abrir `index.html` en el navegador (doble click o con Live Server).
3. Login con: `distribuidor / farmacia2025`.

---

## 🧪 Flujo de Prueba End-to-End

### 1. Producir un lote nuevo

```http
POST http://localhost:8001/laboratorio/producir
Authorization: Basic Auth (laboratorio / farmacia2025)
Content-Type: application/json

{
  "loteId": "LOT-NEW-001",
  "medicamento": "Paracetamol 500mg x 100",
  "categoria": "Analgésico",
  "cantidad": 1000,
  "precioUnitario": 0.50
}
```

### 2. Enviar el lote al distribuidor

```http
POST http://localhost:8001/laboratorio/enviar/LOT-NEW-001
Authorization: Basic Auth
```

### 3. Obtener token JWT (distribuidor)

```http
POST http://localhost:8002/auth/login
Content-Type: application/json

{ "username": "distribuidor", "password": "farmacia2025" }
```

### 4. Despachar el lote a farmacia

```http
POST http://localhost:8002/distribuidor/despachar/LOT-NEW-001
Authorization: Bearer <token-jwt>
```

### 5. Realizar una venta al cliente

```http
POST http://localhost:8003/farmacia/vender/LOT-201?cantidad=3&cliente=Juan Pérez
Authorization: Bearer <token-jwt>
```

### 6. Consultar el reporte consolidado

```http
GET  http://localhost:8004/reportes/resumen
GET  http://localhost:8004/reportes/xml
POST http://localhost:8004/ws  (SOAP getReporteRequest)
```

### 7. Verificar persistencia en MySQL

```sql
USE farmacia_farmacia;
SELECT * FROM venta;

USE farmacia_reportes;
SELECT * FROM reporte_venta;
```

---

## 💊 Datos Precargados (medicamentos peruanos)

| Medicamento | Categoría | Precio (S/.) |
|---|---|---|
| Paracetamol 500mg x 100 | Analgésico | 0.50 |
| Amoxicilina 500mg x 100 | Antibiótico | 3.00 |
| Ibuprofeno 400mg x 100 | Antiinflamatorio | 1.00 |
| Omeprazol 20mg x 30 | Gástrico | 2.50 |
| Loratadina 10mg x 10 | Antihistamínico | 0.80 |
| Aspirina 100mg x 100 | Analgésico | 0.30 |
| Diclofenaco 50mg x 20 | Antiinflamatorio | 0.90 |
| Bisolvón Jarabe 120ml | Antitusivo | 15.50 |
| Panadol Antigripal x 12 | Antigripal | 7.90 |
| Vitamina C 1g x 20 | Vitamina | 4.50 |

---

## ⭐ Características Destacadas

✅ **Arquitectura de microservicios** real con Spring Cloud  
✅ **Service Discovery** con Netflix Eureka  
✅ **Comunicación síncrona** entre servicios usando OpenFeign declarativo  
✅ **Tres esquemas de seguridad distintos**: Basic Auth, JWT, OAuth2  
✅ **Dos protocolos de comunicación**: REST y SOAP/WSDL  
✅ **Database per Service** — cada microservicio dueño de su BD  
✅ **Spring Data JPA + Hibernate** para ORM  
✅ **Persistencia real en MySQL 8**  
✅ **Frontend web** que integra los 5 microservicios  

---

## 🚧 Mejoras Futuras

- [ ] **API Gateway** — Spring Cloud Gateway como punto único de entrada
- [ ] **Circuit Breaker** — Resilience4j para tolerancia a fallos
- [ ] **Tests automatizados** — JUnit + Mockito + Testcontainers
- [ ] **Containerización** — Docker + Docker Compose
- [ ] **Orquestación** — Kubernetes para producción
- [ ] **Observabilidad** — Zipkin/Sleuth para tracing distribuido
- [ ] **Caché distribuida** — Redis
- [ ] **Mensajería asíncrona** — RabbitMQ o Kafka

---

## 📜 Documentación adicional

El proyecto incluye documentación complementaria:

- 📄 **Contrato de APIs** (`Contrato_APIs_Farmacia.docx`)
- 🖼️ **Diagrama de arquitectura lógica** (`diagrama_arquitectura.png`)
- 📊 **Presentación del proyecto** (`Presentacion_Farmacia_Microservicios.pptx`)

---

## 👨‍💻 Autor

Proyecto desarrollado como **proyecto final** del curso de Arquitectura de Microservicios.

📍 **Lima, Perú** · 2026

---

## 🤝 Contribuciones

Este es un proyecto académico. Si deseas usarlo como referencia para tu aprendizaje, ¡adelante! Cualquier sugerencia de mejora es bienvenida vía issues o pull requests.

---

⭐ **Si este proyecto te ayudó, dale una estrellita en GitHub!**
