# Clasificación de minerales con una CNN

Proyecto académico de la asignatura **Ingeniería del Conocimiento (ISO56B)** de la Facultad de Ingeniería de Sistemas de la Universidad Nacional del Centro del Perú. Se explora la clasificación multiclase de fotografías de minerales usando redes neuronales convolucionales (CNN) con Keras 3 y el backend PyTorch.

> El notebook documenta el flujo experimental. Para reproducirlo hacen falta el dataset de Kaggle y las dependencias de Python; GitHub solo aloja el código y no ejecuta el notebook.

## Objetivo

Dada una fotografía de un espécimen, predecir a cuál de estas 15 clases pertenece: azurita, baritina, berilo, calcita, cerusita, cobre, fluorita, yeso, hematita, malaquita, pirita, piromorfita, cuarzo, smithsonita y wulfenita.

El proyecto usa **Mineral Photos**, un dataset de imágenes reales con aproximadamente 39 mil fotografías y clases desbalanceadas. Esta variedad permite estudiar tanto el rendimiento general como las confusiones entre minerales visualmente parecidos.

## Contenido

| Archivo o carpeta | Descripción |
| --- | --- |
| [`actividad2_cnn.ipynb`](./actividad2_cnn.ipynb) | Notebook principal: exploración, preprocesamiento, entrenamiento, comparación y evaluación. |
| [`actividad2_cnn.py`](./actividad2_cnn.py) | Exportación a Python del notebook. |
| [`pyproject.toml`](./pyproject.toml) y [`uv.lock`](./uv.lock) | Configuración del proyecto y archivo de bloqueo de las dependencias declaradas. |
| [`src/`](./src/) | Paquete Python del proyecto. |
| [`.env.example`](./.env.example) | Plantilla opcional de configuración local. |
| [`.gitignore`](./.gitignore) | Excluye el dataset, credenciales y artefactos locales/pesados. |

El dataset, los modelos entrenados y las cachés **no se incluyen en GitHub**.

## Dataset

- **Nombre:** Mineral Photos
- **Autor en Kaggle:** Florian Geillon
- **Fuente:** [kaggle.com/datasets/floriangeillon/mineral-photos](https://www.kaggle.com/datasets/floriangeillon/mineral-photos)
- **Clases:** 15

Descarga el dataset desde Kaggle y deja las carpetas de clase dentro de:

```text
dataset/
└── data/
    └── data/
        ├── azurite/
        ├── barite/
        └── ... (una carpeta por clase)
```

La ruta puede variar según la estructura del archivo descargado. Lo importante es que `dataset/data/data` contenga directamente las carpetas de imágenes por clase, tal como espera el notebook. No subas las imágenes al repositorio: la carpeta `dataset/` está excluida por `.gitignore`.

### Descarga con Kaggle API

La API de Kaggle no conecta automáticamente GitHub con Kaggle. La descarga se ejecuta en la máquina o entorno donde se vaya a correr el notebook, y requiere credenciales propias de Kaggle.

1. Instala y configura la CLI de Kaggle siguiendo la [documentación oficial](https://github.com/Kaggle/kaggle-api). Guarda `kaggle.json` fuera del repositorio y **no lo compartas ni lo subas**.
2. Desde la raíz del proyecto, descarga y descomprime el dataset:

   ```powershell
   python -m pip install kaggle
   kaggle datasets download -d floriangeillon/mineral-photos -p dataset --unzip
   ```

3. Comprueba que la extracción dejó las 15 carpetas de clase en `dataset/data/data`. Si el archivo se descomprimió en otra estructura, mueve o copia esas carpetas a la ruta esperada.

El notebook **no descarga automáticamente** los datos; primero deben estar disponibles localmente en la ruta indicada.

## Requisitos

- Python **3.11 o superior**.
- Jupyter Notebook o JupyterLab.
- Keras 3 con backend PyTorch.
- Dependencias usadas en el notebook: PyTorch, NumPy, pandas, Matplotlib, Pillow, scikit-learn, python-dotenv y tqdm.
- Conexión a internet y credenciales Kaggle solo para descargar el dataset.
- GPU NVIDIA compatible y una instalación de PyTorch con soporte CUDA para entrenamiento en GPU. También se puede ejecutar en CPU, aunque el entrenamiento puede tardar considerablemente más.

> `pyproject.toml` contiene las dependencias del entorno del proyecto, pero no declara todavía todas las bibliotecas de entrenamiento importadas por el notebook. Por eso, instalar el proyecto con `pip install -e .` por sí solo puede no ser suficiente.

## Preparación y ejecución

Desde PowerShell, clona el repositorio y entra a su carpeta:

```powershell
git clone <URL_DEL_REPOSITORIO>
Set-Location <CARPETA_DEL_REPOSITORIO>
```

Crea y activa un entorno virtual, e instala las bibliotecas usadas por el notebook:

```powershell
py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
python -m pip install jupyter keras torch numpy pandas matplotlib pillow scikit-learn python-dotenv tqdm
```

Para usar GPU, instala la variante de PyTorch compatible con tu sistema y CUDA siguiendo el [selector oficial de instalación de PyTorch](https://pytorch.org/get-started/locally/). La disponibilidad de GPU depende del hardware, controladores y versión de PyTorch; el notebook muestra si `torch.cuda.is_available()` es verdadero.

Descarga el dataset como se describe arriba y, desde la raíz del repositorio, inicia Jupyter:

```powershell
jupyter lab
```

Abre `actividad2_cnn.ipynb` y ejecuta las celdas **en orden**, empezando con un kernel limpio. El notebook fija una semilla para facilitar la reproducibilidad, pero los resultados pueden variar según el hardware, las versiones de las bibliotecas y el comportamiento no determinista de algunas operaciones de GPU.

### Ruta del proyecto

El notebook busca el dataset bajo la carpeta del proyecto actual (`Path.cwd()`), por lo que conviene iniciar Jupyter desde la raíz del repositorio. Si necesitas indicar otra ruta, copia `.env.example` como `.env` y define `PROYECTO` con la ruta absoluta al proyecto. El archivo `.env` está ignorado por Git.

## Flujo experimental del notebook

1. **Carga y exploración:** lectura de imágenes y conteo por clase.
2. **Preprocesamiento:** conversión a RGB, redimensionamiento, detección y exclusión de archivos no legibles, eliminación de duplicados y balanceo.
3. **Partición de datos:** conjuntos de entrenamiento, validación y prueba con división estratificada.
4. **CNN base:** entrenamiento y análisis de curvas de accuracy y loss.
5. **CNN mejorada:** aumento de datos y técnicas de regularización, con callbacks de entrenamiento.
6. **Comparación de optimizadores:** comparación experimental bajo la configuración definida en el notebook.
7. **Evaluación:** métricas sobre test, reporte por clase, matriz de confusión, curvas ROC/AUC y análisis de errores.

Las decisiones experimentales y los resultados finales deben consultarse en las celdas y salidas del notebook ejecutado.

## Archivos generados

Al ejecutarse, el notebook puede generar cachés NumPy (`cache_*.npy`), historiales de entrenamiento (`hist_*.json`) y modelos Keras (`*.keras`). Estos artefactos se excluyen del repositorio para evitar subir archivos grandes o específicos de una ejecución. Si necesitas conservarlos, guárdalos localmente o publícalos en un almacenamiento de artefactos adecuado.

## Reproducibilidad y límites

- Ejecuta el notebook desde la raíz del repositorio y conserva la semilla indicada en el código para repetir la partición y el entrenamiento.
- Para reconstruir resultados, registra las versiones del entorno, el hardware y cualquier cambio en los parámetros.
- El dataset no se distribuye con este repositorio; cada persona debe obtenerlo desde Kaggle y respetar sus condiciones de uso.
- GitHub presenta el notebook y sus salidas guardadas, pero no instala dependencias, descarga datos ni ejecuta celdas al abrirlo.

## Autores

Trabajo académico de la Facultad de Ingeniería de Sistemas, Universidad Nacional del Centro del Perú. Completa aquí los nombres y códigos de todos los integrantes antes de publicar la versión final.
