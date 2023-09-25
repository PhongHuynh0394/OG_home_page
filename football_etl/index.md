# Football ETL Analysis

<!--more-->
**Source**
{{< friend name="PhongHuynh0394" url="https://github.com/PhongHuynh0394/Football_ETL_Analysis" avatar="/img/github-logo.png" bio="Football_ETL_Analysis">}}

## Introduce
Trong project này, OG sẽ build end-to-end ETL data pipeline hoàn chỉnh để phân tích football data từ Kaggle với data pipeline như sau:

![data pipeline](https://github.com/PhongHuynh0394/Football_ETL_Analysis/raw/master/images/ETL.png "Data pipeline")

Các bước cụ thể:
1. **Set up**: Dùng `Docker` tạo container và các images cần thiết cho pipeline, trong đó có cả `Dagster` dùng xây dựng pipeline.
1. **Chuẩn bị data source**: load các file csv (có được từ Kaggle) vào `MySQL` nhằm mục đích lưu trữ raw data (mô phỏng source data)
2. **Extract**: Lấy data từ `MySQL` và load vào `MinIO` chuẩn bị cho bước transform
3. **Transform**: Sử dụng `Pandas` để truy vấn các file từ `MinIO`
5. **Load**: Cleaned và transformed data được load vào warehouse `PostgreSQL`
6. **Visualization**: Sử dụng `Streamlit` để làm Dashboard

## Set up
Bắt đầu với `Docker`, ta sẽ xây dựng lần lượt từng image bằng cách viết `docker-compose.yml`

{{< admonition "Tip" "Tip nho nhỏ">}}
Hãy [pull]/[build] lần lượt từng loại framework lần lượt để chắc chắn rằng chúng hoạt động trơn tru nhất trước

Hoặc bạn cũng có thể xem luôn phần hoàn chỉnh [Hoàn chỉnh set up](#hoàn-chỉnh-setup)
{{< /admonition>}}


### MinIO
MinIO là một server lưu trữ đối tượng dạng phân tán với hiệu năng cao và cung cấp các api giống với `Amazon S3`, ta có thể upload, download file,... một cách đơn giản.
```yml
  minio:
      hostname: minio
      image: minio/minio
      container_name: minio
      volumes: 
        - ./MinIO/storage:/data
        - ./MinIO/config:/root/.minio
      ports:
        - "9000:9000"
        - "9090:9090"
      env_file:
        - ./.env
      command: server /data --console-address ":9090"
      networks:
        - de_network
    mc:
      image: minio/mc
      container_name: mc
      hostname: mc
      env_file:
        - ./.env
      entrypoint: >
            /bin/sh -c " until (/usr/bin/mc config host add minio http://minio:9000 minio minio123) do echo '...waiting...' && sleep 1;done; /usr/bin/mc mb minio/warehouse; /usr/bin/mc policy set public minio/warehouse; exit 0; "
      depends_on:
        - minio
      networks:
        - de_network
```
{{< admonition "note" >}}
Về `.env` file, đây là file chứa thông tin các biến môi trường thiết lập cho từng image, mình sẽ nói sau
{{< /admonition>}}

### MySQL
MySQL là một trong số các phần mềm RDBMS (Relational DataBase Management Systems) phổ biến nhất, ta sẽ sử dụng database này để lưu raw data mô phỏng cho source data cần ingest
```yml
  de_mysql:
      image: mysql:8.0
      container_name: de_mysql
      ports:
        - "3306:3306"
      volumes:
        - ./storage/mysql_data:/var/lib/mysql
        - ./dataset:/tmp/dataset
      env_file:
        - ./.env
      networks:
        - de_network
```

### PostgeSQL
PostgreSQL là một hệ thống quản trị cơ sở dữ liệu quan hệ-đối tượng (object-relational database management system), và ta sẽ dung nó làm data warehouse cho project lần này.
```yml
  de_psql:
    container_name: de_psql
    image: postgres:15-alpine
    env_file:
      - ./.env
    ports:
      - '5432:5432'
    volumes: 
      - ./storage/postgres_data:/var/lib/postgresql/data
    networks:
      - de_network    
```

### Dagster
Dagster là một công cụ mã nguồn mở hỗ trợ Orchestrate Task (quản lý, tổ chức, điều phối các tác vụ và công việc), hỗ trợ giúp xây dựng `data pipeline` khá tốt. Mình thấy công cụ này gọn nhẹ hơn `Apache Airflow` và cũng đơn giản hơn.

Đầu tiên hãy viết trong `docker-compose.yml` về dagster như sau:
```yml
  de_dagster:
    build:
      context: ./dagster/
    container_name: de_dagster
    image: de_dagster
```

Đối với Dagster, ta sẽ tự config bằng `Dockerfile`, hãy tạo 1 folder `./dagster/`:
```Bash
mkdir dagster
cd dagster
touch Dockerfile requirements.txt
``` 

Sau đó tạo `Dockerfile` có nội dung sau:
```Dockerfile
# Dagster libraries to run both dagit and the dagster-daemon. Does not
# need to have access to any pipeline code.
FROM python:3.10-slim
# Set $DAGSTER_HOME and copy dagster instance and workspace YAML there
ENV DAGSTER_HOME=/opt/dagster/dagster_home
RUN mkdir -p $DAGSTER_HOME && \
    mkdir -p $DAGSTER_HOME/storage && \
    mkdir -p $DAGSTER_HOME/compute_logs && \
    mkdir -p $DAGSTER_HOME/local_artifact_storage
WORKDIR $DAGSTER_HOME
COPY requirements.txt $DAGSTER_HOME
RUN pip install --upgrade pip && pip install -r requirements.txt
```
và `requirement.txt`:
```text
dagster==1.1.20
dagit==1.1.20
dagster-postgres==0.17.20
dagster-dbt==0.17.20
```

#### Dagit
Dagit là giao diện UI của Dagster, và ta sẽ thao tác trên Dagit để quản lý các assets, jobs,...
```yml
  de_dagster_dagit:
    image: de_dagster:latest
    entrypoint:
      - dagit
      - -h
      - "0.0.0.0"
      - -p
      - "3001"
      - -w
      - workspace.yaml
    container_name: de_dagster_dagit
    expose:
      - "3001"
    ports:
      - "3001:3001"
    volumes: # Make docker client accessible so we can terminate containers from dagit
      - /var/run/docker.sock:/var/run/docker.sock
      - ./dagster_home:/opt/dagster/dagster_home
    env_file:
      - ./.env
    networks:
      - de_network
```
Sau đó hãy tạo 1 folder `./dagster_home`, trong đó tạo 2 file config cho workspace của dagster:
`dagster.yaml`
```yml
run_coordinator:
  module: dagster.core.run_coordinator
  class: QueuedRunCoordinator
  config:
    max_concurrent_runs: 3

scheduler:
  module: dagster.core.scheduler
  class: DagsterDaemonScheduler
  config:
    max_catchup_runs: 5

storage:
  postgres:
    postgres_db:
      username:
        env: DAGSTER_PG_USERNAME
      password:
        env: DAGSTER_PG_PASSWORD
      hostname:
        env: DAGSTER_PG_HOSTNAME
      db_name:
        env: DAGSTER_PG_DB
      port: 5432
run_launcher:
  module: dagster.core.launcher
  class: DefaultRunLauncher
compute_logs:
  module: dagster.core.storage.local_compute_log_manager
  class: LocalComputeLogManager
  config:
    base_dir: /opt/dagster/dagster_home/compute_logs
local_artifact_storage:
  module: dagster.core.storage.root
  class: LocalArtifactStorage
  config:
    base_dir: /opt/dagster/dagster_home/local_artifact_storage
```
và `workspace.yaml`
```yml
load_from:
  - grpc_server:
      host: etl_pipeline
      port: 4000
      location_name: "etl_pipeline"
```
#### Dagster Deamon
Để quản lý các Schedules, sensors,... ta cần phải có `dagster-deamon`
```yml
  de_dagster_daemon:
    image: de_dagster:latest
    entrypoint:
      - dagster-daemon
      - run
    container_name: de_dagster_daemon
    volumes: # Make docker client accessible so we can launch containers using host docker
      - /var/run/docker.sock:/var/run/docker.sock
      - ./dagster_home:/opt/dagster/dagster_home
    env_file:
      - ./.env
    networks:
      - de_network
```

### Pipeline
Tất cả mọi việc xây dựng pipeline ta sẽ hoạt động ở đây

Đầu tiên ta cần init một dagster project
```bash
dagster project scaffold --name etl_pipeline
```
và thư mục mới tạo sẽ trông như thế này:
![tree](./img/projects-tree.png)

{{< admonition tip >}}
Để chạy được lệnh dagster ở bước tạo dagster project, ta cần phải có `dagster package`, nếu chưa có hãy cài đặt bằng: `pip install dagster`

--> Nên cài đặt trong môi trường ảo
{{< /admonition >}}

Sau đó vào thư mục vừa tạo vào viết `Dockerfile` thôi:
```Dockerfile
FROM python:3.10-slim

# Add repository code
WORKDIR /opt/dagster/app

COPY requirements.txt /opt/dagster/app

RUN pip install --upgrade pip && pip install -r requirements.txt

WORKDIR /opt/dagster/app

COPY . /opt/dagster/app/etl_pipeline
# CMD allows this to be overridden from run launchers or executors that want to run other commands against your repository
CMD ["dagster", "api", "grpc", "-h", "0.0.0.0", "-p", "4000", "-m", "etl_pipeline"]
```
cùng với `requirements.txt`:
```
dagster==1.1.20
dagit==1.1.20
dagster-postgres==0.17.20
dagster-aws==0.17.20
dagster-dbt==0.17.20
pandas==1.5.3
SQLAlchemy==1.4.46
pymysql==1.0.2
cryptography==39.0.0
pyarrow==10.0.1
boto3==1.26.57
fsspec==2023.1.0
s3fs==0.4.2
minio==7.1.13
```
Cuối cùng là viết vào `docker-compose.yml`:
```yml
  etl_pipeline:
    build:
      context: ./etl_pipeline
      dockerfile: ./Dockerfile
    container_name: etl_pipeline
    image: etl_pipeline:latest
    volumes:
      - ./etl_pipeline:/opt/dagster/app
    env_file:
      - ./.env
    networks:
      - de_network
```
### Streamlit
Cuối cùng là việc là Dashboard, Streamlit chắc chắc là công cụ siêu phù hợp làm việc này. Đây là framework hỗ trợ việc xây dựng giao diện ưu nhìn chỉ bằng Python, quá đã phải không nào :))

Hãy tạo folder `./streamlit/scr/` cùng với  `./streamlit/Dockerfile`:
```Dockerfile
FROM python:3.10

EXPOSE 8501

WORKDIR /usr/src/app

COPY requirements.txt ./

RUN pip install -r requirements.txt

COPY . .
```
và `streamlit/requirements.txt`:
```text
pandas
plotly
matplotlib
numpy
streamlit
psycopg2-binary
sqlalchemy
python-dotenv
```
Cuối cùng là ghi trong yml
```yml
  streamlit:
    build: ./streamyml
    container_name: streamlit
    image: streamlit:latest
    command: "streamlit run src/main.py"
    ports:
      - "8501:8501"
    volumes:
      - "./streamlit/src:/usr/src/app/src"
    networks:
      - de_network
```

### Hoàn chỉnh setup
Cuối cùng, file yaml sẽ trông như thế này:
```yml
# version: '3.9'
services:
  minio:
    hostname: minio
    image: minio/minio
    container_name: minio
    volumes: 
      - ./MinIO/storage:/data
      - ./MinIO/config:/root/.minio
    ports:
      - "9000:9000"
      - "9090:9090"
    env_file:
      - ./.env
    command: server /data --console-address ":9090"
    networks:
      - de_network
  mc:
    image: minio/mc
    container_name: mc
    hostname: mc
    env_file:
      - ./.env
    entrypoint: >
          /bin/sh -c " until (/usr/bin/mc config host add minio http://minio:9000 minio minio123) do echo '...waiting...' && sleep 1;done; /usr/bin/mc mb minio/warehouse; /usr/bin/mc policy set public minio/warehouse; exit 0; "
    depends_on:
      - minio
    networks:
      - de_network

  de_mysql:
    image: mysql:8.0
    container_name: de_mysql
    ports:
      - "3306:3306"
    volumes:
      - ./storage/mysql_data:/var/lib/mysql
      - ./dataset:/tmp/dataset
    env_file:
      - ./.env
    networks:
      - de_network

  de_psql:
    container_name: de_psql
    image: postgres:15-alpine
    env_file:
      - ./.env
    ports:
      - '5432:5432'
    volumes: 
      - ./storage/postgres_data:/var/lib/postgresql/data
    networks:
      - de_network
    
  streamlit:
    build: ./streamlit
    container_name: streamlit
    image: streamlit:latest
    command: "streamlit run src/main.py"
    ports:
      - "8501:8501"
    volumes:
      - "./streamlit/src:/usr/src/app/src"
    networks:
      - de_network

  de_dagster:
    build:
      context: ./dagster/
    container_name: de_dagster
    image: de_dagster

  de_dagster_dagit:
    image: de_dagster:latest
    entrypoint:
      - dagit
      - -h
      - "0.0.0.0"
      - -p
      - "3001"
      - -w
      - workspace.yaml
    container_name: de_dagster_dagit
    expose:
      - "3001"
    ports:
      - "3001:3001"
    volumes: # Make docker client accessible so we can terminate containers from dagit
      - /var/run/docker.sock:/var/run/docker.sock
      - ./dagster_home:/opt/dagster/dagster_home
    env_file:
      - ./.env
    networks:
      - de_network

  de_dagster_daemon:
    image: de_dagster:latest
    entrypoint:
      - dagster-daemon
      - run
    container_name: de_dagster_daemon
    volumes: # Make docker client accessible so we can launch containers using host docker
      - /var/run/docker.sock:/var/run/docker.sock
      - ./dagster_home:/opt/dagster/dagster_home
    env_file:
      - ./.env
    networks:
      - de_network
    
  # Pipelines
  etl_pipeline:
    build:
      context: ./etl_pipeline
      dockerfile: ./Dockerfile
    container_name: etl_pipeline
    image: etl_pipeline:latest
    volumes:
      - ./etl_pipeline:/opt/dagster/app
    env_file:
      - ./.env
    networks:
      - de_network
  
networks:
  de_network:
    driver: bridge
    name: de_network
```

Và `.env` file: 
```
# PostgreSQL
POSTGRES_HOST=de_psql
POSTGRES_PORT=5432
POSTGRES_DB=football
POSTGRES_USER=admin
POSTGRES_PASSWORD=admin123
POSTGRES_HOST_AUTH_METHOD=trust

# Dagster
DAGSTER_PG_HOSTNAME=de_psql
DAGSTER_PG_USERNAME=admin
DAGSTER_PG_PASSWORD=admin123
DAGSTER_PG_DB=football

# MySQL
MYSQL_HOST=de_mysql
MYSQL_PORT=3306
MYSQL_DATABASE=football
MYSQL_ROOT_PASSWORD=admin123
MYSQL_USER=admin
MYSQL_PASSWORD=admin123

# MinIO
MINIO_ENDPOINT=minio:9000
MINIO_ROOT_USER=minio
MINIO_ROOT_PASSWORD=minio123
MINIO_ACCESS_KEY=minio
MINIO_SECRET_KEY=minio123
DATALAKE_BUCKET=warehouse
AWS_ACCESS_KEY_ID=minio
AWS_SECRET_ACCESS_KEY=minio123
AWS_REGION=us-east-1
```

{{< admonition warning >}}
Nếu bạn chỉ đọc phần [Hoàn chỉnh setup](#hoàn-chỉnh-setup) thì có thể hệ thống vễ sẽ gặp lỗi vì có thể thiếu các configuration cần thiết cho `dagster`, `dagit` hay `pipeline`. Bạn cần đọc qua phần [Dagster](#dagster), [Dagit](#dagit), [Pipeline](#pipeline)
{{< /admonition>}}

**Chạy thử**: sau khi hoàn tất toàn bộ set up dài ngoằn, cũng đã đến lúc chạy chương trình thôi.

{{< admonition note "Note nho nhỏ">}}
Nếu bạn đã build lần lượt các images rồi, thì chỉ cần compose up thôi, nếu không, hãy build bằng lệnh `docker compose build` trước khi chạy compose up.
{{< /admonition>}}

```bash
docker compose --env-file .env up -d
```

{{< admonition tip "Lại là một tip có thể hữu ích">}}
Để đơn giản hóa việc ghi lệnh dài dòng, hãy tạo một make file tên `Makefile` với nội dung sau:

```Makefile
include .env

build:
	docker compose build

up:
	docker compose --env-file .env up -d

down:
	docker compose --env-file .env down

restart:
	make down && make up

to_psql:
	docker exec -ti de_psql psql postgres://${POSTGRES_USER}:${POSTGRES_PASSWORD}@${POSTGRES_HOST}:${POSTGRES_PORT}/${POSTGRES_DB}

psql_create:
	docker exec -ti de_psql psql postgres://${POSTGRES_USER}:${POSTGRES_PASSWORD}@${POSTGRES_HOST}:${POSTGRES_PORT}/${POSTGRES_DB} -f /tmp/psql_schema.sql

to_mysql:
	docker exec -it de_mysql mysql --local-infile=1 -u"${MYSQL_USER}" -p"${MYSQL_PASSWORD}" ${MYSQL_DATABASE}

to_mysql_root:
	docker exec -it de_mysql mysql -u"root" -p"${MYSQL_ROOT_PASSWORD}" ${MYSQL_DATABASE}

mysql_load:
	docker exec -it de_mysql mysql --local_infile -u"${MYSQL_USER}" -p"${MYSQL_PASSWORD}" ${MYSQL_DATABASE} -e"source /tmp/dataset/load_data/load_mysql_data.sql"

mysql_create:
	docker exec -it de_mysql mysql --local_infile -u"${MYSQL_USER}" -p"${MYSQL_PASSWORD}" ${MYSQL_DATABASE} -e"source /tmp/dataset/load_data/mysql_schema.sql"

mysql_create_relation:
	docker exec -it de_mysql mysql --local_infile -u"${MYSQL_USER}" -p"${MYSQL_PASSWORD}" ${MYSQL_DATABASE} -e"source /tmp/dataset/load_data/mysql_foreign.sql"
```

Sau đó chỉ cần `make up` ở terminal là xong. Nó hoạt động như alias vậy.
{{< /admonition >}}


## To be Continue
Bài đến đây cũng quá là dài rồi, mình sẽ viết tiếp ở phần 2 :)))

Chúc bạn một ngày tốt lành 

**-Mew-**

---

# Related Content

{{< showcase "Stock Analysis" "Basic Analyzing VN30 stock using PCA and K-Means" "img/projects-stock.jpg" "/stock_analysis/" >}}

{{< showcase "Football ETL Analysis P2" "Continuous of Football ETL series" "img/projects-football.jpg" "/football_etl_2/" >}}

