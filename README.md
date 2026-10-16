# CI Pipeline para Laravel

## Objetivo

Automatizar la instalación de dependencias y la ejecución de pruebas de la aplicación Laravel mediante GitHub Actions, cada vez que se envían cambios a la rama `main`.

## Tecnologías

- PHP 8.3
- Laravel 13
- Composer
- PHPUnit
- Git y GitHub
- GitHub Actions

## Requisitos

- PHP 8.3 o superior (compatible con el proyecto)
- Composer
- Git
- Acceso al repositorio de GitHub

## Instalación local

1. Clonar el repositorio:
   ```bash
   git clone https://github.com/LeoVD29S/Practica-CI-CD.git
   cd Practica-CI-CD
   ```
2. Instalar dependencias:
   ```bash
   composer install
   ```
   (Si no existe `composer.lock`, usar `composer update`.)
3. Copiar el entorno de ejemplo:
   ```bash
   cp .env.example .env
   ```
4. Generar la clave de la aplicación:
   ```bash
   php artisan key:generate
   ```
5. (Opcional) Crear la base SQLite y migrar:
   ```bash
   touch database/database.sqlite
   php artisan migrate
   ```
6. Ejecutar las pruebas:
   ```bash
   php artisan test
   ```

## Integración continua

El workflow `.github/workflows/ci.yml` se ejecuta automáticamente al hacer *push* a `main` o al abrir un *pull request* hacia esa rama.

### Pasos del pipeline

1. Checkout del código
2. Configuración de PHP 8.3 y extensiones (`mbstring`, `pdo_sqlite`, `sqlite3`, `bcmath`, `fileinfo`)
3. Validación de `composer.json`
4. Instalación de dependencias con Composer
5. Preparación del entorno Laravel (`.env` + `key:generate`)
6. Ejecución de pruebas con `php artisan test` (SQLite en memoria)

## Pruebas automatizadas

| Prueba | Archivo | Qué verifica |
|--------|---------|--------------|
| `test_home_page_is_accessible` | `tests/Feature/HomePageTest.php` | Que la ruta `/` responda con éxito (HTTP 2xx) |
| `test_the_application_returns_a_successful_response` | `tests/Feature/ExampleTest.php` | Que `/` responda con HTTP 200 |

Las pruebas de feature usan SQLite en memoria (`DB_CONNECTION=sqlite`, `DB_DATABASE=:memory:`), configurado en `phpunit.xml` y también en el workflow.

### Resultados esperados

- **Ejecución exitosa:** todas las pruebas pasan y el job `laravel-tests` queda en verde.
- **Ejecución fallida controlada:** si se cambia la aserción a `assertStatus(404)` mientras la ruta responde 200, el pipeline falla y los logs muestran el error de PHPUnit.

## Evidencias

Repositorio: https://github.com/LeoVD29S/Practica-CI-CD

### Ejecuciones de GitHub Actions

- **Exitosa (Prueba A):** https://github.com/LeoVD29S/Practica-CI-CD/actions/runs/38979423622
- **Fallida controlada (Prueba B):** https://github.com/LeoVD29S/Practica-CI-CD/actions/runs/38979923659 (aserción `assertStatus(404)`)
- **Exitosa tras corrección:** https://github.com/LeoVD29S/Practica-CI-CD/actions/runs/38980196231

### Cómo interpretar los resultados

1. Abrir la pestaña **Actions** del repositorio.
2. Seleccionar el workflow **Laravel CI Pipeline**.
3. Abrir el job `laravel-tests` y revisar cada paso.
4. En caso de fallo, leer el primer error relevante en el paso **Ejecutar pruebas automatizadas**.

## Conclusiones

La integración continua detecta errores de forma automática en cada cambio enviado al repositorio. Al provocar un fallo controlado (aserción incorrecta) y luego restaurarlo, se demuestra que el pipeline no solo se ejecuta, sino que también comunica correctamente el estado de las pruebas.

## Autor

Práctica DGS 2026 — Implementación de un CI Pipeline Laravel / PHP con GitHub.
