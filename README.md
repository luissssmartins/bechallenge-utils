# Desafio Web (Deploy)

Este repositório contém os arquivos necessários para realizar o deploy em nuvem, clique (aqui)[http://bechallenge.luis-martins.com/] para acessar um deploy já realizado.

# Dependências necessárias em sua máquina

* Python (versão 3.9 ou superior)
* Node JS (versão 16 ou superior)
* Postgre SQL ou imagem do PostreSQL no Docker

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

# Realizando o deploy em nuvem (front-end + back-end + postgresql)

Para que haja o deploy em nuvem de forma correta, realize os seguintes passos abaixo:

* Primeiramente, vamos buildar nossa imagem Docker do front-end e back-end:

```bash

$ docker compose build

```

* Depois, com todas as dependencias instaladas e a imagem construída, vamos colocar a imagem em funcionamento:

```bash

$ docker compose up

```

