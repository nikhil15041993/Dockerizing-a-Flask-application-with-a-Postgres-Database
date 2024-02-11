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





## Restoring a Dump Backup File to a PostgreSQL Container

This guide provides step-by-step instructions on how to restore a dump backup file to a PostgreSQL container using Docker. If you encounter any errors during the process, troubleshooting tips are provided below.

### Steps:

1. **Copy the dump file into the container:**
   Use the `docker cp` command to copy the dump file into the container. Replace `/path/to/techibeans.dump.sql` with the path to your dump file and `<container_id>` with the ID of your container.

   ```bash
   docker cp /path/to/techibeans.dump.sql <container_id>:/tmp
   ```

2. **Access the PostgreSQL container:**
   Enter the PostgreSQL container using the `docker exec` command with the `-it` option. Replace `<container_id>` with the ID of your container.

   ```bash
   docker exec -it <container_id> bash
   ```

3. **Restore the dump file:**
   Use the `pg_restore` command to restore the dump file. Replace the placeholders with your actual database connection details and the path to the dump file.

   ```bash
   pg_restore --host=localhost --port=5432 --username=myusername --dbname=mydatabase /path/to/techibeans.dump
   ```

### Troubleshooting:

If you encounter errors during the restoration process, refer to the following troubleshooting tips:

- **Role does not exist error:**
  If you receive an error indicating that a role does not exist, create the missing role using the `CREATE ROLE` command. Replace `<role name>` with the name of the missing role.

  ```sql
  CREATE ROLE <role name>;
  ```

- **Role "postgres" does not exist error:**
  If you encounter an error stating that the role "postgres" does not exist, create the "postgres" role with superuser privileges. Replace `'your_password'` with your desired password.

  ```sql
  CREATE ROLE postgres SUPERUSER LOGIN PASSWORD 'your_password';
  ```

- **`CREATE: command not found` error:**
  If you encounter a `CREATE: command not found` error, ensure that you are executing the command within the PostgreSQL container and using the correct syntax.

  ```bash
  psql -U test -d test -c "CREATE ROLE postgres SUPERUSER LOGIN PASSWORD 'test'"
  ```

Feel free to modify this README file according to your specific setup and requirements. If you need further assistance, don't hesitate to reach out to the community or refer to the official documentation for Docker and PostgreSQL.
