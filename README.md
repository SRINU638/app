**Dockerized Python + PostgreSQL Demo App**

This project demonstrates how to run a Python application inside a Docker container that connects to a PostgreSQL database (also running in Docker). The Python app creates a table, inserts sample data, and prints the results in a formatted table.
Project Structure
my-docker-app/
│── app.py
│── Dockerfile
│── README.md

Step 1: Create Docker Network

docker network create mynetwork
(If it already exists, ignore the error.)
<img width="764" height="119" alt="image" src="https://github.com/user-attachments/assets/e626d064-3552-4056-81e5-9454cc374fca" />

Step 2: Run PostgreSQL Container
docker run -d \
  --name my-postgres \
  --network mynetwork \
  -e POSTGRES_USER=user \
  -e POSTGRES_PASSWORD=pass \
  -e POSTGRES_DB=mydb \
  postgres
Check if PostgreSQL is running:
docker ps
<img width="719" height="168" alt="image" src="https://github.com/user-attachments/assets/ae6b9931-18c0-4f79-88f4-ead8ca719f3e" />


Step 3: Python App (app.py)

import psycopg2
import time
from tabulate import tabulate
time.sleep(5)
try:
    conn = psycopg2.connect(
        host="my-postgres",
        database="mydb",
        user="user",
        password="pass"
    )
    cur = conn.cursor()
    cur.execute("""
        CREATE TABLE IF NOT EXISTS artsstudents (
            id SERIAL PRIMARY KEY,
            name VARCHAR(50)
        );
    """)
    cur.execute("INSERT INTO artsstudents (name) VALUES ('Srinivas');")
    cur.execute("INSERT INTO artsstudents (name) VALUES ('Rahul');")
    cur.execute("INSERT INTO artsstudents (name) VALUES ('Priya');")
    cur.execute("SELECT * FROM artsstudents;")
    rows = cur.fetchall()
    print(tabulate(rows, headers=["ID", "Name"], tablefmt="psql"))
    conn.commit()
    cur.close()
    conn.close()
except Exception as e:
    print("Database error:", e)
<img width="823" height="108" alt="image" src="https://github.com/user-attachments/assets/6877eb10-3115-44b2-a6fc-c752e97b9073" />

    
Step 4: Dockerfile
FROM python:3.10-slimDockerized Python + PostgreSQL Demo App
This project demonstrates how to run a Python application inside a Docker container that connects to a PostgreSQL database (also running in Docker). The Python app creates a table, inserts sample data, and prints the results in a formatted table.
Check if PostgreSQL is running:
docker ps
Step 3: Python App (app.py)
import psycopg2
import time
from tabulate import tabulate
time.sleep(5)
try:
    conn = psycopg2.connect(
        host="my-postgres",
        database="mydb",
        user="user",
        password="pass"
    )
    cur = conn.cursor()
    cur.execute("""
        CREATE TABLE IF NOT EXISTS artsstudents (
            id SERIAL PRIMARY KEY,
            name VARCHAR(50)
        );
    """)
    cur.execute("INSERT INTO artsstudents (name) VALUES ('Srinivas');")
    cur.execute("INSERT INTO artsstudents (name) VALUES ('Rahul');")
    cur.execute("INSERT INTO artsstudents (name) VALUES ('Priya');")
    cur.execute("SELECT * FROM artsstudents;")
    rows = cur.fetchall()
    print(tabulate(rows, headers=["ID", "Name"], tablefmt="psql"))
    conn.commit()
    cur.close()
    conn.close()
except Exception as e:
    print("Database error:", e)
Step 4: Dockerfile

FROM python:3.10-slim
WORKDIR /app
COPY app.py .
RUN pip install psycopg2-binary tabulate
CMD ["python", "app.py"]
WORKDIR /app
COPY app.py .
RUN pip install psycopg2-binary tabulate
CMD ["python", "app.py"]
Step 5: Build Your Docker Image
<img width="754" height="73" alt="image" src="https://github.com/user-attachments/assets/52da231a-47ff-4f34-a036-23c32a265465" />

docker build -t my-python-app .
Step 6: Run Your Python App Container
docker run --rm --network mynetwork my-python-app
Expected Output
+----+-----------+
| ID | Name      |
+----+-----------+
|  1 | Srinivas  |
|  2 | Rahul     |
|  3 | Priya     |
+----+-----------+
<img width="330" height="222" alt="image" src="https://github.com/user-attachments/assets/e0885326-e5b9-47be-9d05-aea2c6c66328" />

Cleanup Commands
Stop PostgreSQL:
docker stop my-postgres
Remove container:
docker rm my-postgres
Remove image:
docker rmi my-python-app
You Have Successfully Built:
A Python app inside Docker
A PostgreSQL container
A custom Docker network
Database table creation
Data insertion and formatted output



