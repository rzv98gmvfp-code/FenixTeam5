# ¿Quién abandona el banco y a quién retener primero?

Proyecto final del Diplomado **Liderazgo en la Era de la IA** · Universidad Anáhuac Querétaro · **Equipo 5**

## Integrantes

- César Jair González Zúñiga
- Eliseo Vázquez Palacios
- Fernando Jesús Simón Vázquez

## El problema

Un banco minorista con 10,000 clientes en Francia, Alemania y España pierde a **uno de cada cinco** (2,037 clientes, 20.4%). Cada cliente que se va se lleva su dinero, los intereses de sus créditos, las comisiones y la relación comercial.

La gerencia de retención tiene un número limitado de llamadas, así que la pregunta es: **¿a quién llamar primero?**

Equivocarse cuesta de dos formas (supuestos del equipo):

| Error | Qué pasa | Costo |
|---|---|---|
| Falso negativo | Se va un cliente y no lo detectamos | 1,000 |
| Falso positivo | Llamamos a alguien que no se iba | 100 |

Por eso no medimos con *accuracy* (decir «nadie se va» ya acierta 79.7% y no detecta a nadie), sino con **recall, precisión, AUC, PR-AUC y costo total**:

```
Costo total = clientes no detectados × 1,000 + llamadas de más × 100
```

## Qué hicimos

| Pregunta de negocio | Técnica | Notebook |
|---|---|---|
| ¿Qué se relaciona con irse? | Exploración, intervalos de confianza y chi-cuadrada | Semanas 1 a 3 |
| ¿Quién se va? | Regresión lineal, regresión logística, K-NN y árbol de decisión | Semanas 7 a 9 |
| ¿Quiénes se parecen? | K-Means, PCA, UMAP y DBSCAN | Semanas 7 a 9 y semana 10 |
| ¿A quién retener primero? | Red neuronal en Keras, umbral y presupuesto de llamadas | Semana 11 |
| ¿Dónde se pierde más? | Tablero en Power BI con datos en Microsoft Fabric | Tablero |

## Resultados principales

Con los mismos 2,000 clientes de prueba (407 se fueron) y umbral 0.5:

| Modelo | Recall | Precisión | AUC | PR-AUC | Costo total |
|---|---|---|---|---|---|
| Red neuronal (Keras) | 78% | 0.49 | 0.87 | 0.71 | 124,500 |
| Árbol de decisión | 44% | 0.78 | 0.84 | 0.64 | 232,200 |
| K-NN (K = 29) | 25% | 0.84 | 0.81 | 0.58 | 307,000 |
| Regresión logística | 19% | 0.59 | 0.77 | 0.48 | 336,300 |
| Decir «nadie se va» | 0% | — | 0.50 | 0.20 | 407,000 |

- La red aprende con penalización de clases. Con la misma penalización, el árbol cuesta 124,600: prácticamente empatan.
- Si solo hay presupuesto para llamar al 10% más riesgoso, el 84% de esos clientes sí se iba.
- UMAP + DBSCAN encontró 5 segmentos con abandono de 3% a 86%. El de mayor oportunidad son los inactivos con 1 producto y saldo: se van el doble que los activos (34% contra 17%).

**Límites:** los modelos muestran relaciones, no causas; los costos son supuestos del equipo; no se sabe de qué banco ni de qué años son los datos.

## Estructura del repositorio

> Ajusten los nombres si sus archivos se llaman distinto.

```
├── README.md
├── notebooks/
│   ├── semanas1a3_exploracion_churn.ipynb
│   ├── Proyecto_Churn_Semanas_7_8_9_con_resultados.ipynb
│   ├── semana10_reduccion_dbscan_churn.ipynb
│   └── Equipo_5_Semana_11.ipynb
├── powerbi/
│   ├── (archivo del tablero de Power BI)
│   └── Equipo5_Anexo_PowerBI.docx      # Power Query y las 21 medidas DAX
└── presentacion/
    └── Equipo5_Presentacion.html        # se abre con doble clic en Chrome o Edge
```

El archivo `Churn_Modelling.csv` **no se incluye** en el repositorio, porque su licencia no está declarada. Se descarga de Kaggle (ver abajo).

## Cómo correr el proyecto

### 1. Descargar los datos

1. Entra a [Churn Modelling en Kaggle](https://www.kaggle.com/datasets/shrutimechlearn/churn-modelling) (se necesita una cuenta gratuita).
2. Descarga el archivo `Churn_Modelling.csv` (10,000 filas y 14 columnas).

### 2. Correr los notebooks en Google Colab

1. Abre [Google Colab](https://colab.research.google.com) y elige **Archivo → Subir notebook**.
2. Sube el notebook que quieras correr.
3. Elige **Entorno de ejecución → Ejecutar todas**.
4. Cuando la primera celda lo pida, sube `Churn_Modelling.csv` con el botón **Elegir archivos**.
5. Espera a que terminen todas las celdas. El de la semana 11 es el que más tarda, porque entrena varias redes.

Córrelos en este orden:

| # | Notebook | Qué hace |
|---|---|---|
| 1 | `semanas1a3_exploracion_churn.ipynb` | Limpieza, exploración, intervalos de confianza y chi-cuadrada |
| 2 | `Proyecto_Churn_Semanas_7_8_9_con_resultados.ipynb` | Regresión, clasificación (logística, K-NN y árbol), ROC, PR-AUC y K-Means |
| 3 | `semana10_reduccion_dbscan_churn.ipynb` | PCA, UMAP y DBSCAN: los 5 segmentos |
| 4 | `Equipo_5_Semana_11.ipynb` | Red neuronal en Keras, EarlyStopping, comparación de costos y PR-AUC |

**Notas:**

- Todos usan la **semilla 42**, así que los resultados salen iguales cada vez.
- Colab ya trae pandas, scikit-learn, seaborn, scipy y TensorFlow/Keras. Si en el notebook de la semana 10 aparece un error al importar `umap`, quita el `#` de la línea `!pip install -q umap-learn` y vuelve a correr.
- Para correrlos en tu computadora, pon `Churn_Modelling.csv` en la misma carpeta que los notebooks e instala las librerías:

```bash
pip install pandas numpy matplotlib seaborn scipy scikit-learn umap-learn tensorflow
```

### 3. Abrir el tablero de Power BI

- **Desde la liga del informe:** requiere iniciar sesión con una cuenta que tenga acceso.
- **Desde el archivo:** ábrelo en Power BI Desktop. Si pide actualizar el origen, apúntalo al almacén de Microsoft Fabric o a `Churn_Modelling.csv`. Los 9 pasos de Power Query y las 21 medidas DAX están en `Equipo5_Anexo_PowerBI.docx`.

### 4. Ver la presentación

Abre `Equipo5_Presentacion.html` en Chrome o Edge y pon pantalla completa con **F11**. Avanza con la flecha derecha o la barra espaciadora. No necesita internet, salvo el botón que abre el tablero de Power BI.

## Herramientas

Python · Google Colab · pandas · scikit-learn · scipy · umap-learn · TensorFlow/Keras · Microsoft Fabric · Power BI (Power Query y DAX)

## Fuente de los datos

Iyyer, S. (s.f.). *Churn Modelling* [Conjunto de datos]. Kaggle. https://www.kaggle.com/datasets/shrutimechlearn/churn-modelling

Los datos no tienen nombres ni identificadores (se quitaron `RowNumber`, `CustomerId` y `Surname`). Se usan solo con fines educativos y no comerciales.
