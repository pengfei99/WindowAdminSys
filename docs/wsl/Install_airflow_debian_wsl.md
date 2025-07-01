# Install airflow

## Setup database

Airflow supports many databases:
- sqlite: is only for dev and test
- mysql:
- postgres: 15, 16 is recommended, here **we use 16**.

```shell
CREATE DATABASE airflow_db WITH ENCODING 'UTF8' LC_COLLATE='en_US.UTF-8' LC_CTYPE='en_US.UTF-8' TEMPLATE=template0;
CREATE USER airflow_user WITH PASSWORD 'airflow';
GRANT ALL PRIVILEGES ON DATABASE airflow_db TO airflow_user;

-- PostgreSQL 15 requires additional privileges:
-- Note: Connect to the airflow_db database before running the following GRANT statement
-- You can do this in psql with: \c airflow_db
GRANT ALL ON SCHEMA public TO airflow_user;
```

> The database must use a UTF-8 character set

## Install python 

Here we use pyenv to install **python 3.12**


## Install airflow

```shell
air_root_path=/home/pliu/airflow

AIRFLOW_VERSION=2.10.5

PYTHON_VERSION="$(python -c 'import sys; print(f"{sys.version_info.major}.{sys.version_info.minor}")')"

echo 'AIRFLOW_HOME=$air_root_path' >> ~/.bashrc 
export AIRFLOW_HOME=$air_root_path

CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-${PYTHON_VERSION}.txt"

# the postgres option installs psycopg2-binary and enables PostgreSQL backend support.
pip install "apache-airflow[postgres]==${AIRFLOW_VERSION}" --constraint "${CONSTRAINT_URL}"

# setup the db backend
export AIRFLOW__DATABASE__SQL_ALCHEMY_CONN=postgresql+psycopg2://airflow_user:airflow@localhost:5432/airflow_db

# init db, this will generate the airflow.cfg file
airflow db migrate
```

## Configure airflow 

The main config is located in `airflow.cfg`


## Run airflow
There are breaking changes in 3.0.*. The old commands do not work anymore.
```shell

airflow users create \
    --username pliu \
    --firstname Pengfei \
    --lastname liu \
    --role Admin \
    --email pengfei.liu@casd.eu
    
    
airflow api-server --port 8080

airflow scheduler

airflow dag-processor

airflow triggerer
```