# Proyecto Tlanesi

Proyecto nuevo creado a partir del esqueleto de `proyecto_horizonte_v2`, sin
modificar el repositorio original. Licencia **MIT** (`LICENSE`).

La base heredada es un pipeline de datos **sintéticos**: limpieza de leads,
validación de esquema, forecasting de tipo de cambio MXN/USD con modelos
clásicos y de ML, y generación de reportes. Los nombres y escenarios son
ficticios y las series se generan con semillas fijas (42 y 7).

> El trabajo nuevo de este repositorio es el dashboard web. El pipeline C++17
> no aplica aquí: esta base es Python. El dashboard está pendiente.

## Estructura

```text
Proyecto-Tlanesi/
├── data/                 # CSV de entrada y salida (sintéticos)
├── reports/              # métricas, pronóstico y figuras
├── src/
│   ├── horizonte/        # paquete: cleaning, validation, forecasting, CLI...
│   └── generar_datos.py  # script de compatibilidad
├── tests/                # pytest
├── pyproject.toml        # instalación editable y comando `tlanesi`
├── requirements.txt      # dependencias esenciales
├── requirements-full.txt # modelos opcionales
├── requirements-dev.txt  # pytest
└── LICENSE
```

## Instalación

Python 3.11+ (probado con 3.13). Se instala como paquete editable, así que
**no hace falta configurar `PYTHONPATH`**:

```sh
python -m venv .venv
# Linux/macOS:
. .venv/bin/activate
# Windows PowerShell:
.venv\Scripts\Activate.ps1

pip install -e .
pip install -e '.[dev]'          # añade pytest
# Opcional, si el mirror lo permite:
pip install -e '.[full]'         # statsmodels + xgboost
```

Alternativa sin `pyproject.toml`:

```sh
pip install -r requirements.txt
pip install -r requirements-dev.txt
```

## Uso

Desde la raíz del repositorio:

```sh
tlanesi generate-data
tlanesi validate --dataset leads --input data/leads_ventas.csv
tlanesi clean --input data/leads_ventas.csv --output data/leads_ventas_clean.csv
tlanesi forecast --input data/fx_historico.csv --output-dir reports
tlanesi report-figures --output-dir reports
tlanesi run-all
tlanesi run-all --skip-figures
```

También funciona como módulo: `python -m horizonte.cli --help`.

## Pruebas

```sh
pytest -q
```

## Salidas principales

- `data/leads_ventas_clean.csv`: leads limpios y normalizados.
- `reports/metricas_fx.csv`: MAE/RMSE/MAPE por modelo.
- `reports/pronostico_6m.csv`: pronóstico a 6 meses.

## Notas

- Sin `statsmodels`, ARIMA se sustituye por un random walk con deriva; sin
  `xgboost`, el modelo de ML usa `GradientBoostingRegressor` de scikit-learn.
  En ambos casos el pipeline corre y las pruebas pasan.
- `click` se fija en `>=8.1,<8.2`: las versiones de Typer compatibles con este
  esqueleto rompen con Click 8.3 (falla el parseo de opciones y el `--help`).
- Los datos son generados con semillas fijas para reproducibilidad.
- No hay credenciales, datos personales ni secretos en el repositorio.
