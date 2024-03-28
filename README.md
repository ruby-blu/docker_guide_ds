# Docker 

## Introduction

> Docker is to containers as Google is to search

'A container is a special type of process that is isolated from other processes. Containers are assigned resources that no other process can access, and they cannot access any resources not explicitly assigned to them.' The advantage of containers is that a company can create an isolated computer within a computer, which provides security and consistency. [raygun.com](https://raygun.com/blog/what-is-docker/#:~:text=In%20conclusion%2C%20Docker%20is%20popular,create%20vast%20economies%20of%20scale.) 

### What are containers?

'Containers sit on top of a physical server and its host OS—for example, Linux or Windows. Each container shares the host OS kernel and, usually, the binaries and libraries, too. Shared components are read-only. Containers are thus exceptionally “light”—they are only megabytes in size and take just seconds to start, versus gigabytes and minutes for a Virtual Machines.'

'Containers also reduce management overhead. Because they share a common operating system, only a single operating system needs care and feeding for bug fixes, patches, and so on. In short, containers are lighter weight and more portable than VMs.' [blog.netap.com](https://blog.netapp.com/blogs/containers-vs-vms/)

### Why Docker?

'Docker enables developers to easily pack, ship, and run any application as a lightweight, portable, self-sufficient container that can run virtually anywhere. Containers give you instant application portability.'    

Containers do this by enabling developers to isolate code into a single container. This makes it easier to modify and update the program. It also lends itself, as Docker points out, for enterprises to break up big development projects among multiple smaller Agile teams to automate the delivery of new software in containers.[zdnet.com](https://www.zdnet.com/article/what-is-docker-and-why-is-it-so-darn-popular/)

> Docker is to containers as GitHub is to Git

Docker brings several new things to the table that the earlier technologies didn't. The first is it's made containers easier and safer to deploy and use than previous approaches. In addition, because of Docker's partnering with the other container powers, including Canonical, Google, Red Hat, and Parallels, on its critical open-source component `libcontainer`, it's brought much-needed standardization to containers.

Since then, Docker has donated "its software container format and its runtime, as well as the associated specifications," to The Linux Foundation's Open Container Project. Specifically, "Docker has taken the entire contents of the `libcontainer` project, including `nsinit`, and all modifications needed to make it run independently of Docker, and donated it to this effort." [zdnet.com](https://www.zdnet.com/article/what-is-docker-and-why-is-it-so-darn-popular/)

### Docker for data science?

Using docker containers means you don't have to deal with "works on my machine" problems. Generally, the main advantage Docker provides is standardization. This means you can define the parameters of your container once and run it wherever Docker is installed. The Docker process provides a few significant advantages:

1. __Reproducibility:__ Everyone has the same OS, the same versions of tools etc. If it works on your machine, it works on everyone's machine.
2. __Portability:__ This means that moving from local development to a super-computing cluster is easy. Also, if you're working on open-source data science projects, you can provide collaborators with an easy way to bypass setup hassles.
3. __Docker Hub:__ You can take advantage of the community to find pre-built images [search here](https://hub.docker.com/search?q=data%20science&type=image)

Another huge advantage – learning to use Docker will make you a better engineer or turn you into a data scientist with superpowers. Many systems rely on Docker, and it will help you turn your ML projects into applications and deploy models into production. [dagshub.com](https://dagshub.com/blog/setting-up-data-science-workspace-with-docker/)

## Getting started

1. [Install Docker Desktop](https://www.docker.com/get-started) (Windows users will need to [install WSL-2](windows_wsl2.md).)
2. [Create a Dockerhub account](https://hub.docker.com/signup)
3. [Fork this repo](https://github.com/byuibigdata/docker_guide_streamlit) with the clone your forked version to your desktop.
4. Within the cloned repository, Open a terminal and switch the working directory to one of the two `docker-` directories. For example, using `cd docker-streamlit` will get you into the correct folder for streamlit.
  - The [jupyter/all-spark-notebook](https://hub.docker.com/r/jupyter/all-spark-notebook) could be used by using `cd docker-spark`.
5. Within the respective `docker-` folder in your terminal you can now run `docker compose up` to take advantage of the `docker-compose.yaml` file within the directory.


_Note that the command line versions require that the full local volume path is specified. We will be able to use relative file paths with the yaml._

## Streamlit App

After opening a terminal in the directory `~/docker-streamlit` and running `docker compose up` you should see action in the containers section of Docker and your terminal. 

Now you can open your streamlit app at [http://localhost:8501](http://localhost:8501)

### Developing your App

Microsft's Visual Studio code provides guidance on [developing inside a Container using Visual Studio Code Remote [Development](https://code.visualstudio.com/docs/devcontainers/containers). Let's use their [get started with development Containers in Visual Studio Code](https://code.visualstudio.com/docs/devcontainers/tutorial) tutorial.

Now we can have a VS Code window running on the OS environment within the container. 

## Spark-Notebook

After opening a terminal in the directory `~/docker-spark` and running `docker compose up` you should see a lot of action in the containers section of Docker and your terminal. 

Now open [http://localhost:8888/lab?token=easy](http://localhost:8888/lab?token=easy). Our token is set to `easy` which is not recommended in development.


### Starting Spark

You can use the `example.ipynb` script in the `scripts` folder of your container. It contains the code shown below.

```python
# Databricks handles the first two imports.
import os
from pyspark.sql import SparkSession 
from pyspark import SparkConf 
# Will need to execute this on Databricks
from pyspark.sql import functions as F # access to the sql functions https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.functions

warehouse_location = os.path.abspath('../data/spark-warehouse')
java_options = "-Dderby.system.home=" + warehouse_location

conf = (SparkConf()
    .set("spark.ui.port", "4041")
    .set('spark.jars', '/home/jovyan/scratch/postgresql-42.2.18.jar')
    .set("spark.driver.memory", "7g")  
    .set("spark.sql.warehouse.dir", warehouse_location) # set above
    .set("hive.metastore.schema.verification", False)
    .set("javax.jdo.option.ConnectionURL", "jdbc:derby:;databaseName=metastore_db;create=true")
    .set("javax.jdo.option.ConnectionDriverName", "org.apache.derby.jdbc.EmbeddedDriver")
    .set("javax.jdo.option.ConnectionUserName", 'userman')
    .set("jdo.option.ConnectionPassword", "pwd")
    .set("spark.driver.extraJavaOptions",java_options)
    .set("spark.sql.inMemoryColumnarStorage.compressed", True) # default
    .set("spark.sql.inMemoryColumnarStorage.batchSize",10000) # default
    )

spark = SparkSession.builder \
    .master("local") \
    .appName('test') \
    .config(conf=conf) \
    .getOrCreate()

spark.conf.set("spark.sql.execution.arrow.pyspark.enabled", "true")
```


## References

- [raygun.com](https://raygun.com/blog/what-is-docker/#:~:text=In%20conclusion%2C%20Docker%20is%20popular,create%20vast%20economies%20of%20scale.)
- [blog.netap.com](https://blog.netapp.com/blogs/containers-vs-vms/)
- [zdnet.com](https://www.zdnet.com/article/what-is-docker-and-why-is-it-so-darn-popular/)
- [dagshub.com](https://dagshub.com/blog/setting-up-data-science-workspace-with-docker/)
- [backup and restore postgresql](https://docs.bitnami.com/installer/infrastructure/mapp/administration/backup-restore-postgresql/) and [docker and postgresql](https://markheath.net/post/exploring-postgresql-with-docker)
