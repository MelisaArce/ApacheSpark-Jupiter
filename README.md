# 🚀 Spark + Jupyter en Docker Compose

Este proyecto configura un entorno distribuido de Apache Spark junto con Jupyter Notebook usando Docker Compose. Todos los servicios comparten un volumen para facilitar la lectura de archivos.

## 📂 Estructura del Repositorio
```
.
├── data/                  # Carpeta con archivos CSV u otros datos
├── docker-compose.yml      # Configuración de los contenedores
└── notebooks/             # Notebooks de Jupyter
```

## 🛠️ Servicios Incluidos
- **Spark Master** (`spark-master`)
- **Spark Workers** (`spark-worker-1`, `spark-worker-2`)
- **Jupyter Notebook** (`jupyter`)

## 🚀 Cómo Usarlo
### 1️⃣ Levantar los contenedores
Ejecuta el siguiente comando en la terminal:
```sh
docker compose up -d
```
Esto iniciará los contenedores en segundo plano.

### 2️⃣ Acceder a Jupyter Notebook
Para obtener el token de acceso a Jupyter, ejecuta:
```sh
docker logs jupyter | grep "token="
```
Luego, abre en tu navegador:
```
http://localhost:<JUPYTER_PORT>?token=<TOKEN>
```
(Sustituye `<JUPYTER_PORT>` por el puerto configurado en `.env` o `docker-compose.yml` y `<TOKEN>` por el valor obtenido en el comando anterior).

### 3️⃣ Leer archivos en Spark desde Jupyter
Coloca los archivos en la carpeta `data/` y usa el siguiente código en un notebook:
```python
from pyspark.sql import SparkSession

spark = SparkSession.builder\
    .master("spark://spark-master:7077")\
    .appName("EDA")\
    .getOrCreate()

# Cargar CSV desde la carpeta compartida
path = "/data/archivo.csv"
df = spark.read.csv(path, header=True, inferSchema=True)
df.show()
```

### 4️⃣ Detener los contenedores
Si necesitas apagar los contenedores, ejecuta:
```sh
docker compose down
```

## 🛠️ Solución de Problemas
- **No encuentra los archivos CSV:** Asegúrate de que están en `./data` antes de iniciar los contenedores.
- **Error de conexión en Spark:** Verifica que el Spark Master está corriendo con `docker ps` y revisa los logs con `docker logs spark-master`.
- **No puedes acceder a Jupyter:** Asegúrate de obtener el token con `docker logs jupyter | grep "token="` y usarlo en la URL de acceso.

## 📝 Notas
- La configuración usa `docker-compose.yml` y se recomienda agregar un archivo `.env` para definir los puertos de manera personalizada.
- Los volúmenes compartidos aseguran que todos los servicios accedan a los mismos datos sin necesidad de mover archivos manualmente.

