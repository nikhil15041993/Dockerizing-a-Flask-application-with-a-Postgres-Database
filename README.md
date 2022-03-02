# Dockerizing-a-Flask-application-with-a-Postgres-Database

1. Creating the flask application
2. Create requirements.txt
3. Create a Dockerfile
4. Containerizing the services


## Create requirements.txt

You can do it by running ``` pip freeze > requirements.txt``` will save all your python libraries with current version into requirements.txt file.


## Create a Dockerfile
```
----------------


FROM python:3.8.0-buster

# Make a directory for our application
WORKDIR /application
# Install dependencies
COPY requirements.txt .
RUN pip install -r requirements.txt

# Copy our source code
COPY . /application

# Run the application
CMD ["python", "main.py"]

```


## Containerizing the services

```
--------------------



version: '3.3'
services:
  flask-application:
    container_name: techibeans
    build:
      context: .
      dockerfile: Dockerfile
      #   env_file:
           #- env.list
    environment:  
      SQLALCHEMY_DATABASE_URI=postgresql+psycopg2://test:test@postgresql/test
    restart: always
    
    depends_on:
      - postgresql
    
    ports:
      - 5000:5000  



  postgresql:
    container_name: postgresql 
    image: postgres:latest
    restart: always
    environment:
      - POSTGRES_USER=test
      - POSTGRES_PASSWORD=test
      - POSTGRES_DB=test

    ports:
      - '5432:5432'
    volumes: 
      - db4:/var/lib/postgresql/data

    tty: true
      

volumes:
  db4:
    driver: local   
    
 ```


For connect our flask app to postgress database container service  we just copy the flowing code to ``` app.config['SQLALCHEMY_DATABASE_URI'] ``` part in flask app.
```
app.config['SQLALCHEMY_DATABASE_URI'] = os.environ.get('SQLALCHEMY_DATABASE_URI')
```


