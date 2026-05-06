# fuentes-cs-sociales

# 🗂️ Datos Abiertos para Ciencias Sociales — Argentina

Una guía práctica de fuentes de datos para politólogos, sociólogos e investigadores. Si necesitás datos electorales, censales, del mercado de trabajo, acá encontrás dónde buscarlos y cómo acceder.

> **¿Cómo está organizado?** Cada fuente tiene una descripción breve, para qué sirve, qué tipo de datos tiene y el link directo. Si usás R o Python, también hay snippet de acceso.

---

## 📑 Índice

- [🗳️ Datos electorales](#️-datos-electorales)
- [👥 Mercado de trabajo y condiciones de vida](#-mercado-de-trabajo-y-condiciones-de-vida)
- [🏘️ Datos censales y territoriales](#️-datos-censales-y-territoriales)

---

## 🗳️ Datos electorales

---

### DINE — Dirección Nacional Electoral (datos.gob.ar)
**→ https://datos.gob.ar/dataset?tags=elecciones**

Resultados oficiales del escrutinio provisorio a nivel de mesa, disponibles para múltiples elecciones (2011–2025). Tiene API compatible con OpenAPI v3.

**Útil para:** análisis a nivel de mesa o circuito, datos muy desagregados territorialmente.

**API:** https://datos.gob.ar/dataset/dine_2/archivo/dine_2.1
### DataCP
**→ https://www.datacp.ar/**

Plataforma de la Carrera de Ciencia Política (UBA) que centraliza resultados electorales nacionales de Argentina. Tiene resultados presidenciales y legislativos, composición del Congreso, comparativas históricas entre elecciones y descarga en CSV y GeoJSON.

**Útil para:** análisis de tendencias electorales, mapas de resultados, comparación entre distritos, trabajo con coaliciones y bancas.

**Tiene:**
- Resultados por provincia y sección electoral
- Serie histórica (múltiples elecciones)
- GeoJSON de secciones electorales para mapas
- Tutoriales en R, Python y Excel

**Descargar datos:** https://www.datacp.ar/ → *Descargar datos*

```r
# En R — cargar CSV descargado de DataCP
library(readr)
resultados <- read_csv("resultados_legislativas_2025.csv")
```

```python
# En Python
import pandas as pd
df = pd.read_csv("resultados_legislativas_2025.csv")
```

---

## 👥 Mercado de trabajo y condiciones de vida

### EPH — Encuesta Permanente de Hogares (INDEC)
**→ https://www.indec.gob.ar/indec/web/Institucional-Indec-BasesDeDatos**

Principal fuente para el análisis del mercado de trabajo y las condiciones socioeconómicas en Argentina. Encuesta trimestral a 31 aglomerados urbanos, con microdatos individuales y de hogar disponibles desde 1996.

**Útil para:** tasas de actividad, empleo y desempleo; análisis de ingresos y desigualdad; pobreza e indigencia; estudios longitudinales con datos de panel.

**Tiene:**
- Base individual y base hogar
- Variables de empleo, ingresos, educación, vivienda
- Ponderadores para estimaciones representativas
- Desde 1996 en adelante (EPH continua)

**Explorador interactivo (Shiny):** https://estacionr.shinyapps.io/shiny_eph_panel/

```r
# En R — paquete eph (recomendado)
install.packages("eph")
library(eph)

base <- get_microdata(year = 2024, trimester = 2, type = "individual")
base <- organize_labels(base, type = "individual")

# Tabulado de condición de actividad por sexo
calculate_tabulates(base, x = "ESTADO", y = "CH04", add.percentage = "col")
```

**Documentación del paquete:** https://docs.ropensci.org/eph/ | https://github.com/ropensci/eph

---

## 🏘️ Datos censales y territoriales

### Censo Nacional 2022 (INDEC / censoargentino)
**→ https://censoapp.vercel.app/**

Datos del Censo 2022 a nivel de radio censal para todo el país. Accesibles programáticamente vía el paquete `censoargentino` (Python), que consulta los datos desde Hugging Face. El campo `id_geo` de cada radio se puede cruzar directamente con el GeoJSON de radios censales del INDEC.

**Útil para:** análisis territoriales desagregados, mapas socioeconómicos, NBI, acceso a servicios, composición demográfica por barrio o partido.

**Tiene:**
- Sexo, grupos de edad, nivel de instrucción
- NBI, Índice de Privación Material del Hogar (IPMH)
- Acceso a internet, tipo de vivienda
- Nivel: radio censal (la unidad más pequeña disponible)

```python
# En Python — paquete censoargentino
pip install censoargentino

import censoargentino as censo
import pandas as pd

variables = ["PERSONA_P02", "PERSONA_EDADGRU", "HOGAR_NBI_TOT"]

dfs = [censo.query(variables=v, provincia="Buenos Aires", departamento="Vicente López") for v in variables]
datos = pd.concat(dfs, ignore_index=True)
datos.to_csv("datos_censo.csv", index=False)
```

```r
# En R — cruzar con GeoJSON
library(sf); library(dplyr); library(readr)

censo  <- read_csv("datos_censo.csv")
geo    <- st_read("radios-censales.geojson") |> filter(NOMDEPTO == "VICENTE LOPEZ")
geo_censo <- geo |> left_join(censo_wide, by = c("LINK" = "id_geo"))
```

**Paquete Python:** https://pypi.org/project/censoargentino/ | https://github.com/matuteiglesias/censoargentino

---

*Compilado por Milagros Milia· Última actualización: mayo 2026· Contribuciones bienvenidas vía Issues o PR*
