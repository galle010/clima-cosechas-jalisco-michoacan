# Clima por ciclo agrícola – Jalisco y Michoacán

Dataset con la información climática de los **238 municipios** que conforman Jalisco (125) y Michoacán (113), resumida por **municipio, año y ciclo agrícola**, para cruzarse con datos de producción agrícola del SIAP.

- **Periodo:** 2003 – 2025 (incluye además el ciclo Otoño-Invierno 2026)
- **Registros:** 16,660
- **Archivo:** `clima_ciclos.csv`

## Fuentes

- **Clima:** [NASA POWER](https://power.larc.nasa.gov/) – datos diarios, comunidad agrícola (AG).
- **Límites municipales:** CONABIO (con base en el Marco Geoestadístico del INEGI), obtenidos de [mexico-geojson](https://github.com/PhantomInsights/mexico-geojson). Para cada municipio se usó un punto representativo dentro de su territorio.

## Ciclos agrícolas

Se usan los mismos ciclos e ids que el SIAP:

| idciclo | ciclo | Días que resume |
|---|---|---|
| 1 | Otoño-Invierno | 1 oct del año anterior → 31 mar |
| 2 | Primavera-Verano | 1 abr → 30 sep |
| 3 | Perennes | 1 ene → 31 dic |

Solo se incluyen ciclos completos.

## Columnas

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

## Uso

```python
import pandas as pd

URL = "https://raw.githubusercontent.com/USUARIO/REPOSITORIO/main/clima_ciclos.csv"
clima = pd.read_csv(URL, dtype={"cvegeo": str})
```

Para unirlo con datos del SIAP: `cvegeo` + `anio` + `idciclo`.

## Notas

- NASA POWER tiene una resolución aproximada de 50 km, por lo que municipios vecinos pueden compartir valores similares.
- La lluvia de NASA POWER es menos precisa que la temperatura a escala municipal.
