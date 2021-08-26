# Spark Docker Setup

This project is a simple setup to start working with Spark and PySpark.
It uses the [Bitnami Docker image][1] and the `docker-compose.yml` file
provided by the maintainers. It will run one master node and two worker nodes.
The image does not come with PySpark or Jupyter, so those will be installed
with the `Dockerfile`

## Run

```shell
$ docker-compose up
```

You can now access the Spark UI console at `localhost:8080` or access Spark
from the Docker container.

## Using the Master node

```shell
$ docker ps
CONTAINER ID   IMAGE                      NAMES
01289b564537   spark-docker-setup_spark   spark-docker-setup_spark_1
$ docker exec -it spark-docker-setup_spark_1 bash
root@01289b564537:/opt/bitnami/spark#
```

### Python Virtual Environment

The `Dockerfile` set the required environment variables for the virtual
environment to work. So there is no need to run `.venv/bin/activate`.
If you would like to also create the virtual environment locally in your
development environment, you can do so by running the following

```shell
$ python3 -m venv .venv
$ source .venv/bin/activate
(.venv) $ pip install -r config/requirements.txt
```

### Start Jupyter lab

From the Spark master container run

```shell
root@01289b564537:/opt/bitnami/spark# cd work
root@01289b564537:/opt/bitnami/spark/work# ./jupup.sh
...
    Or copy and paste one of these URLs:
        http://01289b564537:8888/lab?token=<TOKEN>
     or http://127.0.0.1:8888/lab?token=<TOKEN>
```

You can access JupyterLab by copying the URL (with token) and pasting it
into your browser. Open `first_notebook.ipynb` and run the first cell

```python
from pyspark.sql import SparkSession
from pyspark.sql.functions import round, col

spark = SparkSession.builder.getOrCreate()

df = spark.read.csv('orders.csv', header=True, schema='id int, order int, total float')
df = df.groupBy('id')\
    .sum('total')\
    .withColumnRenamed('sum(total)', 'total')

df.select('id', round(df['total'], 2))\
    .withColumnRenamed('round(total, 2)', 'total')\
    .show()

+---+-------+
| id|  total|
+---+-------+
| 31|4765.05|
| 85|5503.43|
| 65|5140.35|
| 53| 4945.3|
| 78|4524.51|
| 34| 5330.8|
| 81|5112.71|
| 28|5000.71|
| 76|4904.21|
| 27|4915.89|
| 26| 5250.4|
| 44|4756.89|
| 12|4664.59|
| 91|4642.26|
| 22|5019.45|
| 93|5265.75|
| 47| 4316.3|
|  1| 4958.6|
| 52|5245.06|
| 13|4367.62|
+---+-------+
only showing top 20 rows
```



[1]: https://hub.docker.com/r/bitnami/spark