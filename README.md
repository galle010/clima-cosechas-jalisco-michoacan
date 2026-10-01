# Clima y cosechas – Jalisco y Michoacán

Datasets de producción agrícola (SIAP) y clima (NASA POWER) para los municipios de Jalisco y Michoacán, resumidos por **municipio, año y ciclo agrícola**, listos para analizar la relación entre clima y producción.

## Archivos

| Archivo | Contenido | Registros |
|---|---|---|
| `cultivos.csv` | Producción agrícola del SIAP (frijol, aguacate y café cereza) | 8,877 |
| `clima_ciclos.csv` | Clima de los 238 municipios por año y ciclo agrícola | 16,660 |
| `cultivos_clima.csv` | Unión de los dos anteriores (left join: cultivos + su clima) | 8,877 |

Para análisis se recomienda usar directamente `cultivos_clima.csv`.

## Uso

```python
import pandas as pd

BASE = "https://raw.githubusercontent.com/galle010/clima-cosechas-jalisco-michoacan/main/"

datos    = pd.read_csv(BASE + "cultivos_clima.csv", dtype={"cvegeo": str})
cultivos = pd.read_csv(BASE + "cultivos.csv")
clima    = pd.read_csv(BASE + "clima_ciclos.csv", dtype={"cvegeo": str})
```

> Leer siempre `cvegeo` como texto (`dtype={"cvegeo": str}`) para que la llave empate entre archivos.

---

## 1. `cultivos.csv` – Producción agrícola (SIAP)

Registros del Servicio de Información Agroalimentaria y Pesquera (SIAP) integrados por el equipo para la región de estudio.

- **Periodo:** 2003 – 2025
- **Municipios con registros:** 224 (Jalisco 117, Michoacán 107)
- **Cultivos:** Frijol (ciclos Otoño-Invierno y Primavera-Verano), Aguacate y Café cereza (Perennes)
- **Modalidad:** Riego y Temporal
- **Fuente:** [SIAP](https://www.gob.mx/siap)

### Columnas

| Columna | Descripción | Unidad |
|---|---|---|
| `Anio` | Año agrícola | – |
| `Idestado` / `Nomestado` | Clave y nombre del estado | – |
| `Idddr` / `Nomddr` | Clave y nombre del Distrito de Desarrollo Rural | – |
| `Idcader` / `Nomcader` | Clave y nombre del Centro de Apoyo al Desarrollo Rural | – |
| `Idmunicipio` / `Nommunicipio` | Clave y nombre del municipio | – |
| `Idciclo` / `Nomcicloproductivo` | Clave y nombre del ciclo (1, 2, 3) | – |
| `Idmodalidad` / `Nommodalidad` | Clave y nombre de la modalidad (Riego / Temporal) | – |
| `Idunidadmedida` / `Nomunidad` | Unidad de medida de la producción | Tonelada |
| `Idcultivo` / `Nomcultivo` | Clave y nombre del cultivo | – |
| `Sembrada` | Superficie sembrada | ha |
| `Cosechada` | Superficie cosechada | ha |
| `Siniestrada` | Superficie siniestrada (perdida) | ha |
| `Volumenproduccion` | Volumen de producción | ton |
| `Rendimiento` | Rendimiento (volumen / superficie cosechada) | ton/ha |
| `Precio` | Precio por tonelada (con datos 2003 – 2020) | $/ton |
| `Valorproduccion` | Valor de la producción (volumen × precio) | $ |
| `Preciomediorural` | Precio por tonelada (con datos 2021 – 2025) | $/ton |

> El SIAP reporta el mismo precio en `Precio` hasta 2020 y en `Preciomediorural` desde 2021. En `cultivos_clima.csv` ambas se unifican en una sola columna `precio`.

---

## 2. `clima_ciclos.csv` – Clima por ciclo agrícola

Información climática de los **238 municipios** que conforman Jalisco (125) y Michoacán (113).

- **Periodo:** 2003 – 2025 (incluye además el ciclo Otoño-Invierno 2026)
- **Fuente clima:** [NASA POWER](https://power.larc.nasa.gov/) – datos diarios, comunidad agrícola (AG).
- **Límites municipales:** CONABIO (con base en el Marco Geoestadístico del INEGI), obtenidos de [mexico-geojson](https://github.com/PhantomInsights/mexico-geojson). Para cada municipio se usó un punto representativo dentro de su territorio.

### Ciclos agrícolas

Se usan los mismos ciclos e ids que el SIAP:

| idciclo | ciclo | Días que resume |
|---|---|---|
| 1 | Otoño-Invierno | 1 oct del año anterior → 31 mar |
| 2 | Primavera-Verano | 1 abr → 30 sep |
| 3 | Perennes | 1 ene → 31 dic |

Solo se incluyen ciclos completos.

### Columnas

| Columna | Descripción | Unidad |
|---|---|---|
| `cvegeo` | Clave geoestadística del INEGI (2 dígitos de estado + 3 de municipio) | – |
| `estado` | Estado | – |
| `municipio` | Nombre del municipio | – |
| `anio` | Año del ciclo | – |
| `idciclo` | Id del ciclo (1, 2, 3) | – |
| `ciclo` | Nombre del ciclo | – |
| `t_max_prom` | Promedio de la temperatura máxima diaria | °C |
| `t_media_prom` | Promedio de la temperatura media diaria | °C |
| `t_min_prom` | Promedio de la temperatura mínima diaria | °C |
| `t_max_abs` | Temperatura más alta registrada en el ciclo | °C |
| `t_min_abs` | Temperatura más baja registrada en el ciclo | °C |
| `dias_calor` | Días con temperatura máxima > 35 °C | días |
| `dias_helada` | Días con temperatura mínima < 0 °C | días |
| `lluvia_total_mm` | Lluvia acumulada en el ciclo | mm |
| `dias_lluvia` | Días con lluvia > 1 mm | días |
| `humedad_prom` | Humedad relativa promedio | % |
| `viento_prom` | Velocidad promedio del viento a 2 m | m/s |
| `radiacion_prom` | Radiación solar promedio en superficie | MJ/m²/día |

---

## 3. `cultivos_clima.csv` – Dataset unido

Cada registro de `cultivos.csv` con el clima de su municipio, año y ciclo.

- **Tipo de unión:** left join (cultivos a la izquierda)
- **Llave:** `cvegeo` + año + ciclo
  - `Anio` ↔ `anio`, `Idciclo` ↔ `idciclo`
- **Columnas (37 en total):** las de cultivos + `cvegeo` + `precio` + las 12 variables climáticas.

### Transformaciones aplicadas

- **`cvegeo`:** creada en cultivos como `Idestado * 1000 + Idmunicipio`, en texto de 5 dígitos.
- **`precio`:** une `Precio` (2003 – 2020) y `Preciomediorural` (2021 – 2025); las dos columnas originales se eliminan.
- **Columnas repetidas:** se quitaron de clima `estado`, `municipio`, `ciclo`, `anio` e `idciclo`, porque ya vienen en cultivos.

### Validaciones

- Mismo número de filas antes y después de unir (8,877): no se perdieron ni duplicaron registros.
- Relación muchos a uno verificada (`validate="many_to_one"`): cada municipio-año-ciclo tiene un solo registro de clima.
- 0 registros sin clima.
- 7 registros sin `precio` ni `Rendimiento` (2024 – 2025): municipios con siembra pero sin cosecha (volumen 0), por lo que es correcto que no tengan precio.

---

## Notas

- NASA POWER tiene una resolución aproximada de 50 km, por lo que municipios vecinos pueden compartir valores similares.
- La lluvia de NASA POWER es menos precisa que la temperatura a escala municipal.
- El clima de 2026 no aparece en `cultivos_clima.csv` porque el SIAP aún no publica el cierre agrícola de ese año.
