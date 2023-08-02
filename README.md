# Desafio Web (Deploy)

Este repositório contém os arquivos para realizar o deploy em uma máquina de hospedagem.

# Docker Compose

```bash

version: '3'

services:

  frontend:
    build:
      context: ./bechallenge-frontend # Altere aqui onde está o caminho do projeto front-end, neste caso estou utilizando na pasta raiz.
    ports:
      - "3000:80"
    depends_on:
      - backend

  backend:
    build:
      context: ./bechallenge-backend # Altere aqui onde está o caminho do projeto back-end, neste caso estou utilizando na pasta raiz.
    ports:
      - "8000:8000"
    depends_on:
      - db
    environment:
      - DATABASE_URL=postgres://postgres:postgres@db:5432/postgres
    command: sh -c "python manage.py migrate && python manage.py runserver 0.0.0.0:8000"

  db:
    image: postgres
    expose:
      - "5432"
    ports:
      - "5432:5432"
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres

```

# Dockerfile (Front-end)

```bash

FROM node:16 as build

WORKDIR /app

COPY package*.json ./
RUN npm install --force

COPY . .

RUN npm run build

FROM nginx:alpine

COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]

```

# Dockerfile (Back-end)

```bash

FROM python:3.9

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]

```