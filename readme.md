# Manual de Configuración — Pensión La Herradura

## Requisitos previos

Docker instalado



---

## Estructura del proyecto

```
PROYECTO FINAL/
├── docker-compose.yml       ← Contenedores
├── nginx/
│   └── default.conf         ← Configuración del servidor web
├── php/
│   └── Dockerfile           ← PHP con la extensión Laravel
└── src/                     ← Esa carpeta se consigue con el paso 4
```

---

## Pasos de instalación

### 1. Clonar el repositorio

```bash
git clone https://github.com/Darkoscurito/InstalacionDocker.git
cd PROYECTO FINAL
```

### 2. Levantar el contenedor PHP

```bash
docker compose up -d php
```

Verificar que los contenedores están activos:

```bash
docker compose ps
```

Se deben ver los contenedores `postgres` en estado **Healthy** y `php` en estado **Running**.

### 3. Instalar Laravel

```bash
docker compose exec php composer create-project laravel/laravel .
```

> Este comando descarga Laravel y todas sus dependencias dentro de `src/`.


### 4. Configurar el archivo de entorno

```bash
# Windows (PowerShell)
Copy-Item src\.env.example src\.env

# Mac / Linux
cp src/.env.example src/.env
```

Editar `src/.env` y cambiar los siguientes valores:

```env
DB_CONNECTION=pgsql
DB_HOST=postgres
DB_PORT=5432
DB_DATABASE=hotel_db
DB_USERNAME=hotel_user
DB_PASSWORD=hotel_pass
```

> El valor de `DB_HOST` debe ser `postgres` (nombre del servicio en Docker),
> no `localhost`.


### 5. Levantar el resto de servicios

```bash
docker compose up -d --build
```

> Si hay problemas de puertos tendrás que cambiar con el archivo docker-compose.yml

### 6. Ejecutar las migraciones

```bash
docker compose exec php php artisan migrate
```

### 7. Instalar dependencias

```bash
cd src
npm install
```

> Instalar dependencias (Vue, Bootstrap, Vite...)

### 8. Compilar modo desarrollo

```bash
npm run dev
```



---

## URLS de acceso

| Servicio | URL | Credenciales |
|---------|-----|-------------|
| Aplicación web | http://localhost:8080
| pgAdmin (base de datos) | http://localhost:8081 | admin@admin.com / admin |

**Conexión a la base de datos desde pgAdmin:**

| Campo | Valor |
|-------|-------|
| Host | postgres |
| Puerto | 5432 |
| Base de datos | hotel_db |
| Usuario | hotel_user |
| Contraseña | hotel_pass |

---

Con esto debes conseguir de que se puede ver welcome.blade.php usando http://localhost:8080.