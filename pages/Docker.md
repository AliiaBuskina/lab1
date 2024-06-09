Для запуска приложения в Docker-е сначала я создала файл requirements.txt, в котором прописаны все библиотеки, используемые в проекте
```commandline
pip freeze > requirements.txt
```

Далее был написан Dockerfile:
```dockerfile

FROM python:3.10-slim

RUN mkdir /fastapi_app

WORKDIR /fastapi_app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

Также был написан doker-compose.yml:
```dockerfile
version: "3.8"

services:
  db:
    image: postgres:14
    container_name: db_app
    ports:
      - "5433:5432"
    environment:
      POSTGRES_DB: todo
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: Aliya2103
    volumes:
      - postgres_data:/var/lib/postgresql/data

  fastapi:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: fastapi_app
    ports:
      - "8000:8000"
    depends_on:
      - db
    environment:
      DATABASE_URL: postgresql://postgres:Aliya2103@db:5432/todo

volumes:
  postgres_data:
```

Далее командами:
```commandline
docker compose build
docker compose up
```
Были запущены 2 сервиса для развертывания приложения в Docker-е.