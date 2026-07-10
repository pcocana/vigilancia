# Sistema de Vigilancia Científica — PUCV

Sistema de vigilancia tecnológica/científica para el monitoreo automatizado de la producción académica de investigadores del **Instituto de Matemática, Pontificia Universidad Católica de Valparaíso (PUCV)**.

Integra **Scopus API**, **ORCID API** y **ANID Dataciencia** para recopilar métricas bibliométricas (h-index, citas, publicaciones, cuartiles SJR, proyectos ANID) y genera automáticamente reportes en Excel, JSON, HTML y PDF.

---

## ✨ Características

- **Entrada mínima**: solo se requiere Nombre · Apellido · Unidad Académica por investigador. Los IDs (Scopus Author ID, ORCID iD) se descubren automáticamente.
- **Descubrimiento automático de IDs** vía Scopus Author Search y ORCID Public Search.
- **Recopilación de métricas**: h-index, citas, documentos, publicaciones, coautores, proyectos ANID.
- **Cuartiles SJR** a partir del índice Scimago (CSV local), con fallback a Scopus Serial API.
- **Caché inteligente** de publicaciones y cuartiles para evitar descargas repetidas (configurable por días de refresco).
- **Múltiples salidas**: Excel multi-hoja, JSON, dashboard HTML interactivo y PDF ejecutivo tipo dashboard institucional (KPIs, ranking de productividad, gráficos, alertas y anexo de publicaciones).

---

## 📁 Estructura del proyecto

```
vigilancia-cientifica/
├── Vigilancia3.py                  # Script principal
├── Planilla_Investigador_a.xlsx    # Entrada: Nombre · Apellido · UA (requerido)
├── scimagojr_2025.csv              # Índice de cuartiles SJR (recomendado)
├── index.html                      # Dashboard HTML (embebe datos + localStorage)
│
├── cache_publicaciones.json        # Caché DOI → publicación (autogenerado)
├── cache_cuartiles.json            # Caché ISSN → cuartil (autogenerado)
├── vigilancia.log                  # Log de ejecución (autogenerado)
│
└── data/
    └── json/                       # Almacenamiento persistente por investigador
```

### Archivos de entrada

| Archivo | Requerido | Descripción |
|---|:---:|---|
| `Planilla_Investigador_a.xlsx` | ✅ | Planilla con columnas Nombre · Apellido · Unidad Académica (opcionalmente Scopus ID / N° Orcid ya conocidos). El script no se ejecuta si este archivo no existe. |
| `scimagojr_2025.csv` | Recomendado | Índice ISSN → cuartil descargado de [scimagojr.com](https://www.scimagojr.com/). Si falta, el sistema recurre a Scopus Serial API (más lento). Actualizar anualmente. |

### Archivos generados automáticamente

| Archivo | Descripción |
|---|---|
| `Boletin_Vigilancia_v3.xlsx` | Reporte Excel multi-hoja |
| `datos_dashboard.json` | Datos consolidados para el dashboard HTML |
| `ids_descubiertos.xlsx` | IDs (Scopus/ORCID) descubiertos automáticamente — revisar antes de reutilizar |
| `Informe_Vigilancia_{año}_{mes}.pdf` | Informe ejecutivo tipo dashboard |
| `Informe_Vigilancia_{año}_{mes}.html` | Versión HTML del informe |
| `cache_publicaciones.json` / `cache_cuartiles.json` | Cachés internos |
| `vigilancia.log` | Log de ejecución |

---

## 🔧 Requisitos

- Python 3.9+
- Dependencias:

```bash
pip install pandas requests openpyxl reportlab
```

- Claves de API configuradas dentro de `Vigilancia3.py`:
  - `SCOPUS_API_KEY` (Elsevier/Scopus)
  - `OPENALEX_API_KEY` (opcional, aumenta el rate limit de OpenAlex)

> ⚠️ Las claves de API están actualmente embebidas en el script. Se recomienda migrarlas a variables de entorno o un archivo `.env` (no versionado) antes de publicar el repositorio.

---

## 🚀 Uso

### Ejecución estándar

```bash
python Vigilancia3.py
```

Esto ejecuta el flujo completo:

1. Carga la planilla de investigadores.
2. Descubre IDs faltantes (Scopus / ORCID).
3. Recopila métricas y publicaciones (Scopus + ORCID + ANID).
4. Genera Excel, JSON, HTML y PDF.

### Limpieza de caché

```bash
# Elimina todo el caché (publicaciones y cuartiles)
python Vigilancia3.py --limpiar-cache

# Elimina solo el caché de cuartiles
python Vigilancia3.py --limpiar-cache-cuartiles

# Elimina el caché de publicaciones de un investigador específico
python Vigilancia3.py --limpiar-cache-investigador "Nombre Parcial"
```

---

## 📊 Dashboard HTML

`index.html` funciona como visor autónomo:

- Embebe los datos actuales (`DATOS_EMBEBIDOS`) para funcionar sin carga manual.
- Usa `localStorage` como capa de datos de mayor prioridad (permite agregar investigadores sin tocar el archivo).
- Botón **"Actualizar con nuevo JSON"** para cargar el `datos_dashboard.json` de una nueva corrida.
- Permite agregar nuevos investigadores vía carga de Excel.

---

## 📄 Informe PDF ejecutivo

Generado con **ReportLab**, incluye:

- Portada y KPIs generales (investigadores, publicaciones, variación respecto del período anterior).
- Indicadores bibliométricos (por investigador, año y tipo de documento).
- Ranking de productividad.
- Gráficos: barras (producción anual), horizontal (por investigador), circular (tipo documental), línea (evolución temporal).
- Alertas y hallazgos (nuevas publicaciones, investigadores sin producción reciente, inconsistencias).
- Anexo completo de publicaciones (autores, título, año, fuente, DOI, tipo).

---

## 🗺️ Roadmap

- [ ] Almacenamiento permanente en SQLite (`/data/json/`) con CLI para gestión de investigadores.
- [ ] Opción "Actualizar Investigadores" (reemplazar, agregar, eliminar sin reprocesar todo).
- [ ] Migrar API keys a variables de entorno.

---

## 🏛️ Institución

Instituto de Matemática — Pontificia Universidad Católica de Valparaíso (PUCV)
