# Almacén de Obra – Etapa 1: Datos Generales del Proyecto

Sistema de escritorio de control de almacén en obra (Python + PySide6 + SQLite), solución original
con flujo de trabajo equivalente a IncoAlmacén PRO. Esta entrega implementa la base del sistema y el
módulo **Datos Generales del Proyecto**.

## Instalación (Windows)

1. Instalar Python 3.11 o superior (marcar *Add Python to PATH*).
2. En la carpeta del proyecto:
   ```
   pip install -r requirements.txt
   python main.py
   ```
3. Pruebas automáticas (no requieren interfaz): `python -m unittest -v`

### Generar ejecutable (.exe) – sin Python en las PC de uso
- **Opción A:** en una PC con Python, doble clic en `CONSTRUIR_EXE.bat`. Deja `AlmacenObra.exe`
  (un solo archivo) en la carpeta; ese .exe se copia y abre en cualquier PC con Windows 10/11.
- **Opción B (sin instalar nada):** subir la carpeta a GitHub; el flujo
  `.github/workflows/construir-exe.yml` construye el .exe en la nube (pestaña *Actions*).

## Dónde se guardan los datos
`%APPDATA%\AlmacenObra\almacen.db` (base SQLite) y `%APPDATA%\AlmacenObra\logos\`.
Se puede cambiar con la variable de entorno `ALMACEN_DATA_DIR`.
**Copia de seguridad manual** (hasta que se implemente el módulo de respaldos): cerrar el programa y
copiar la carpeta `AlmacenObra` completa.

## Módulo Datos Generales

| Sección | Contenido |
|---|---|
| 1. Empresa / Entidad | Tipo, razón social, RUC (valida dígito verificador SUNAT), dirección, teléfono, correo, logo |
| 2. Obra | Nombre, nombre corto, CUI, modalidad (administración directa / contrata), contratista y contrato, fuente de financiamiento, presupuesto, estado |
| 3. Ubicación | Departamento, provincia, distrito, localidad |
| 4. Responsables | Residente (obligatorio), supervisor/inspector, almacenero, administrador: nombre, profesión, colegiatura, DNI, teléfono, correo |
| 5. Fechas y plazo | Entrega de terreno, inicio, plazo (días calendario), término programado y real; plazo ↔ término se calculan entre sí; avance temporal |
| 6. Reportes | Moneda y símbolo, denominación del año, línea adicional de encabezado, pie de página, mostrar logo, firmas que aparecen |

Funciones: varios proyectos con **uno activo** (el que usará el almacén), vista previa en vivo del
encabezado de reportes, indicador de completitud, **ficha PDF** e **impresión con vista previa**,
aviso de cambios sin guardar, validación con resaltado de campos, atajos `Ctrl+S`, `Ctrl+N`, `Ctrl+P`,
auditoría de cada cambio (tabla `auditoria`, con valores antes/después) y diseño adaptable (2 o 1 columnas).

`app/services/encabezado_reporte.py` genera encabezado, firmas y pie; todos los reportes de las
siguientes etapas lo reutilizarán.

## Estructura
```
main.py
app/
  config.py                     rutas de datos
  database/connection.py        SQLite, transacciones, migraciones (PRAGMA user_version)
  database/schema_v1.sql        tablas proyecto, proyecto_responsable, auditoria
  core/models.py                modelos y catálogos
  core/validators.py            RUC, CUI, DNI, correo, plazos
  services/proyecto_service.py  reglas de negocio, guardado transaccional, auditoría, logo
  services/encabezado_reporte.py HTML de encabezado/firmas/ficha
  ui/theme.py                   estilos
  ui/widgets/comunes.py         tarjeta, fecha opcional
  ui/modules/datos_generales.py pantalla del módulo
  ui/main_window.py             menú lateral y navegación
tests/                          pruebas unitarias
```

Los demás módulos del menú aparecen deshabilitados hasta su etapa de desarrollo.
