# Лабораторная работа  №2
**Студент:** Liao Xin, M4150

**Вариант:** №2	MYSQL

**Цель работы:**

Получить навыки выгрузки исходных данных и отправки результатов модели с использованием различных источников данных согласно варианту задания.


**Структура каталогов:**
```
C:.
│   .dvcignore
│   .gitignore
│   dataset.dvc
│   docker-compose.yml----------------------  :конфигурация создания контейнера и образа модели;
│   Dockerfile----------------------  :конфигурация создания контейнера и образа модели;
│   requirements.txt----------------------  : используемые зависимости (библиотеки) и их версии;
│
├───.dvc
│   │   .gitignore
│   │   config
│
├───CD
│       Jenkinsfile
│
├───CI
│       Jenkinsfile
│
├───config
│       config.ini: гиперпараметры модели;
│       config.py
│
├───dataset
│       test_features.npy
│       test_ids.csv
│       train_features.npy
│       train_ids.csv
├───mysql
│       databse_test.sql
│       databse_train.sql
│       Dockerfile
│
├───notebooks
│       youtube8m_classfication.ipynb
│
└───src
    │   data.py
    │   model.py
    │   predict.py
    │   train.py
    │
    ├───unit_tests
    │       test_preprocess.py
    │       test_training.py
    │       __init__.py

```

## Задание:

1. Создать репозитории-форк модели на GitHub, созданной в рамках лабораторной работы №1, регулярно проводить commit + push в ветку разработки, важна история коммитов.
2. Реализовать взаимодействие сервиса модели и базы данных, согласно варианту задания.
3. Возможно наполнить базу данных наборами для обучения/валидации модели.
4. Создать CI pipeline (Jenkins, Team City, Circle CI и др.) для сборки docker image и отправки его на DockerHub,   сборка должна автоматически стартовать по pull request в основную ветку репозитория модели;
5. Создать CD pipeline для запуска контейнера и проведения функционального тестирования по сценарию, запуск должен стартовать по требованию или расписанию или как вызов с последнего этапа CI pipeline;



## Наполнить базу данных:
```
# mysql/Dockerfile
FROM mysql:latest
COPY ./databse_train.sql /docker-entrypoint-initdb.d/
COPY ./databse_test.sql /docker-entrypoint-initdb.d/
```

## Connect mysql: 
```
# src/data.py
def connect2mysql():
    user_value=os.getenv("DB_USER")
    password_value=os.getenv("DB_PASS")
    host_value='mysql'
    database_value= os.getenv("NAME_DATABASE")
    type_connect=False
    try:
        connection = mysql.connector.connect(
            user=user_value, password=password_value, host=host_value, port='3306', database=database_value)
        print("mysql connected")
        type_connect=True
    except:
        return type_connect,None
    return type_connect,connection
```
## Test connect:
    ```
    #src/unit_tests/test_preprocess.py
        def test_db_connect(self):
            type_res,connection = connect2mysql()
            self.assertTrue(type_res)
            connection.close()
    ```
   result:
   ![conect_tests](https://github.com/liaoxin-a/big_data_lab2/blob/main/images/connecttest.PNG)
   

## CI/CD:
![CI](https://github.com/liaoxin-a/big_data_lab2/blob/main/images/CI.PNG)
![CD](https://github.com/liaoxin-a/big_data_lab2/blob/main/images/CD.PNG)

## docker hub:
![hub](https://github.com/liaoxin-a/big_data_lab2/blob/main/images/docker%20hub.PNG)

