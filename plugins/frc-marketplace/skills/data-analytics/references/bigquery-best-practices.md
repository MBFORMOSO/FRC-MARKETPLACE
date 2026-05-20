# BigQuery — Buenas prácticas para el equipo FRC

## Antipatrones a evitar

- **SELECT \***: nunca usar en tablas grandes. Seleccionar solo las columnas necesarias.
- **Sin filtro de partición**: siempre filtrar por la columna de partición (generalmente `ds` o `created_date`) para reducir bytes procesados.
- **Subconsultas anidadas sin CTE**: preferir CTEs (`WITH`) para legibilidad y reutilización.
- **CROSS JOIN sin condición**: puede generar explosión de filas. Verificar siempre.
- **Funciones en columnas de filtro**: `WHERE DATE(timestamp_col) = ...` no usa partición. Preferir `WHERE timestamp_col BETWEEN ...`.

## Control de costos

- Hacer siempre un dry run antes de ejecutar queries pesadas.
- Estimar bytes procesados con `--dry_run` o el validador de BQ en la consola.
- Usar `LIMIT` en exploración, pero recordar que LIMIT no reduce bytes escaneados.
- Preferir tablas en `meli-bi-data` o `datamesh` según el dominio.

## Particionado estándar en MELI

- Las tablas operacionales suelen estar particionadas por `ds` (string `YYYYMMDD`) o `created_date`.
- Siempre incluir `WHERE ds = '20240101'` o un rango `BETWEEN` en queries sobre tablas grandes.

## Estructura recomendada de queries analíticas

```sql
-- 1. CTEs para preparar los datos
WITH base AS (
  SELECT
    col1,
    col2,
    SUM(metric) AS total_metric
  FROM `project.dataset.table`
  WHERE ds BETWEEN '20240101' AND '20240131'  -- filtro de partición
  GROUP BY 1, 2
),

-- 2. Enriquecimiento o joins
enriched AS (
  SELECT
    b.*,
    d.label
  FROM base b
  LEFT JOIN `project.dataset.dim_table` d USING (col1)
)

-- 3. Resultado final
SELECT *
FROM enriched
ORDER BY total_metric DESC
LIMIT 100;
```

## Servicios de BigQuery en MELI

- **Fury BQ**: para datos operacionales de aplicaciones Fury
- **Datamesh**: catálogo de datos por dominio de negocio
- **meli-bi-data**: tablas consolidadas para análisis de negocio
