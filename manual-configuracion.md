# Manual de Configuración — Pensión La Herradura

## Requisitos previos

| Herramienta | Versión mínima | Descarga |
|-------------|---------------|---------|
| Docker Desktop | 4.x | https://www.docker.com/products/docker-desktop |
| Git | 2.x | https://git-scm.com |
| VS Code | 1.x | https://code.visualstudio.com |

> No es necesario tener PHP, Composer ni Node.js instalados en la máquina.
> Todo corre dentro de los contenedores Docker.

---

## Estructura del proyecto

```
PROYECTO FINAL/
├── docker-compose.yml       ← Orquestación de contenedores
├── nginx/
│   └── default.conf         ← Configuración del servidor web
├── php/
│   └── Dockerfile           ← Imagen PHP 8.3 con extensiones de Laravel
└── src/                     ← Código fuente de Laravel (se genera en el paso 4)
```

---

## Pasos de instalación

### 1. Clonar el repositorio

```bash
git clone https://github.com/tu-usuario/pension-la-herradura.git
cd pension-la-herradura
```

### 2. Crear las carpetas necesarias

```bash
mkdir nginx php src
```

### 3. Levantar el contenedor PHP

```bash
docker compose up -d php
```

Verificar que los contenedores están activos:

```bash
docker compose ps
```

Se deben ver los contenedores `postgres` en estado **Healthy** y `php` en estado **Running**.

### 4. Instalar Laravel

```bash
docker compose exec php composer create-project laravel/laravel .
```

> Este comando descarga Laravel y todas sus dependencias dentro de `src/`.
> Tarda unos minutos dependiendo de la conexión a internet.

### 5. Configurar el archivo de entorno

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

> ⚠️ El valor de `DB_HOST` debe ser `postgres` (nombre del servicio en Docker),
> no `localhost`.

### 6. Generar la clave de la aplicación

```bash
docker compose exec php php artisan key:generate
```

### 7. Levantar el resto de servicios

```bash
docker compose up -d --build
```

### 8. Ejecutar las migraciones

```bash
docker compose exec php php artisan migrate
```

---

## URLs de acceso

| Servicio | URL | Credenciales |
|---------|-----|-------------|
| Aplicación web | http://localhost:8080 | — |
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

## Solución de problemas frecuentes

**"Project directory is not empty"**

La carpeta `src/` tiene archivos de una instalación anterior. Borrar el contenido e intentar de nuevo:

```bash
# Windows (PowerShell)
Remove-Item -Recurse -Force src\*

# Mac / Linux
rm -rf src/*
```

**Los contenedores no arrancan**

Ver los logs del contenedor con error:

```bash
docker compose logs php
docker compose logs postgres
```

**Laravel no conecta con la base de datos**

Verificar que `DB_HOST=postgres` en el `.env` y que el contenedor de PostgreSQL está en estado Healthy:

```bash
docker compose ps
```
