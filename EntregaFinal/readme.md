# Laboratorio 1 · Entrega final · Modalidad Gerencial

**Francisco Medina Raed** · Competencia Kaggle `labo-1-virtual-2026-manager`

Predicción de los clientes que se dan de baja en **septiembre de 2021 (202109)**. La entrega es un semillerío de 100 modelos LightGBM que contacta a los **1.000** clientes con mayor probabilidad de baja.

---

## Cómo reproducir la entrega

1. Abrir `entrega/z719_final_gerencial_FMR_entrega_7198.ipynb` en Jupyter con kernel **R** (se corrió en una VM de Google Cloud).
2. Correr las celdas en orden. Tarda unos 10 a 15 minutos.
   - Si no encuentra el dataset, lo descarga de `https://storage.googleapis.com/open-courses/austral2026-5da5/labo1/gerencial_competencia_2026.csv.gz`.
   - Para subir a Kaggle necesita `~/.kaggle/kaggle.json`. Para solo generar los archivos, poner `subir_kaggle <- FALSE` en la primera celda.
3. El resultado queda en `~/buckets/b1/exp/WF7198_final/` (o `~/exp/WF7198_final/` si no hay bucket):
   - `kaggle/KA7198_final_1000.csv` es el archivo entregado. Tiene que ser idéntico a `entrega/KA7198_final_1000.csv`.
   - `prediccion.txt`, `impo.txt` y `PARAM.yml` con toda la configuración.

**Versiones usadas** (ver `sesion/sessionInfo.txt`):

| | Versión |
|---|---|
| R | _completar_ |
| lightgbm | _completar_ |
| data.table | _completar_ |

Con otra versión de LightGBM el modelo puede salir distinto aun con los mismos datos y semillas.

**Semilla primigenia:** 123457. Las 100 semillas del semillerío se derivan de ella dentro del notebook.

---

## Qué cambia respecto del workflow z719

| Etapa | z719 | Esta entrega |
|---|---|---|
| Meses de training | 202005 a 202106 | **202103 a 202107** (modelo final) |
| Variables | Las originales con lags y deltas | Además: **ratios**, **montos rankeados por mes** y **tendencias de 6 meses**; sin `numero_de_cliente`, `foto_mes` ni `kmes` |
| Hiperparámetros | Grid por AUC, una semilla | Elegidos **por ganancia** con 10 a 40 semillas: learning rate 0,02, 256 hojas, mínimo 128 por hoja, 150 árboles |
| Modelo final | `c(fijos, mejores)`: el grid no se aplicaba | Corregido con `modifyList` |
| Semillas | 1 | **Semillerío de 100** |
| Corte | Todos los de 800 a 1.300 | **1.000 envíos** |

El notebook de la entrega explica cada cambio, el feature engineering bloque por bloque y los experimentos que llevaron a cada decisión.

---

## Experimentos

Cada decisión se tomó midiendo la ganancia en meses con clase conocida (sobre todo julio, 202107), con varias semillas y comparaciones de a pares. El Public de Kaggle se usó como control.

| Corrida | Qué se probó | Resultado | Notebook |
|---|---|---|---|
| Exp. Colab. 07 | Meses de pandemia en el training | Entrenar desde 202103 gana en 10 de 10 semillas (p = 0,002) | [repositorio del Grupo A](https://github.com/Facundolondero/ExpColaborativo07_GrupoA) |
| WF7190 | Arreglo de `modifyList`, semillerío de 20, V3 contra V2 | V3 mejor; hojas de 32 clientes sobreajustan | `z719_final_gerencial_FMR.ipynb` |
| WF7191 | Grid por ganancia en lugar de AUC | Mejora en la zona del corte (dentro del ruido) | `..._v2_ganancia.ipynb` |
| WF7192 | Feature engineering y sin identificadores | **+3 M en julio**, +4,6 en el Public | `..._v3_FE.ipynb` |
| WF7193 | Grid con 10 semillas por combinación | Toda la grilla en 1 M: los hiperparámetros casi no importan | `..._v3_FE.ipynb` |
| WF7194 | Diez ratios más y canaritos | Peor: los canaritos descartaron de más | `..._v4_ratios_canaritos.ipynb` |
| WF7195 | Diez ratios más, sin canaritos | Sin mejora (−0,24 M) | `..._v4_ratios_canaritos.ipynb` |
| WF7196 | Barrido de 7 hiperparámetros | Ninguno mejora | `..._v5_barrido_7196.ipynb` |
| WF7197 | Learning rate 0,02 contra 0,03, 40 semillas | **+0,48 M** (2,9 errores) | `..._v7_learning_rate_combinaciones_7197.ipynb` |
| WF7198 | Semillerío de 100 y curva de semillas | Estable desde unas 20 semillas | `..._v8_semillerio100_7198.ipynb` |
| WF7199 | Validación a dos meses de distancia | Corte óptimo cerca de 820; meseta de 660 a 1.120 | `..._v9_validacion_2meses_7199.ipynb` |

Los resultados de cada corrida (grids, importancia de variables, `PARAM.yml`, puntajes de Kaggle, curvas y gráficos) están en `experimentos/resultados/`.

**Notas sobre los registros**
- El notebook `v3_FE` se actualizó entre el WF7192 y el WF7193 (de 3 a 10 semillas por combinación en el grid). La versión del repositorio es la del WF7193. La configuración con la que corrió el WF7192 está en su `PARAM.yml`.
- Los notebooks experimentales son reanudables y controlan que no se mezclen resultados de configuraciones distintas.

---

## Estructura

```
entrega/
  z719_final_gerencial_FMR_entrega_7198.ipynb   notebook que reproduce la entrega
  KA7198_final_1000.csv                         archivo subido a Kaggle
  PARAM.yml, impo.txt                           configuración e importancia de variables
experimentos/
  notebooks/                                    notebooks de las corridas WF7190 a WF7199
  resultados/                                   resultados de cada corrida
sesion/
  sessionInfo.txt                               versiones de R y de los paquetes
```

No se incluyen el dataset, las predicciones por semilla ni `kaggle.json`.
