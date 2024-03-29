# Curso de Docker

> ## Menu
>
>
> - [Home](README.md)
> - [Containers](#containers)
> - [Volumes](#volumes)
> - [Imagens](#imagens)
> - [Otimizando imagens](#otimizando-imagens)
> - [Networks](#networks)
> - [Compose](#compose)
> - [Dependência entre containers](#dependência-entre-containers)
> - [Substituir o Docker Desktop por Colima](#colima)

---

### Containers

#### Executando um container

```console
docker run -d -p 8080:80 --name nginx nginx:latest
```

#### Detalhando os parametros

- ` -d ` executa o container sem atachar o console
- ` -p 8080:80 ` aqui estamos mapeando a porta 8080 do host com a porta 80 do container, com isso conseguimos acessar o nginx através do nosso host.
- ` --name nginx ` aqui definimos um nome para o container, isso facilita o gerenciamento do container
  
#### Executando um container em modo iterativo `-it` e acessando o bash

```console
docker run -it ubuntu:latest bash
```

#### Entrando no modo iterativo de um container em execução, o nome do container é nginx

```console
docker exec -it nginx bash
```

#### Listando os containers em execução

```console
docker ps
```

#### Listando os containers suspensos

```console
docker ps -a
```

#### Parando um container

```console
docker stop nginx
```

#### Removendo um container

```console
docker rm nginx
```

#### Forçando a remoção do container

```console
docker rm nginx -f
```

#### Removendo todos os containers ativos e inativos

```console
docker rm $(docker ps -aq) -f
```

#### Verificando os logs

```console
docker log container_name

docker logs -f --tail 1000 nginx
```

#### Listando os arquivos em um container

```console
docker exec nginx ls
```

---

### Volumes

Os volumes servem para compartilhar dados entre o host e o container, para fazer esse compartilhamento podemos usar o `--mount`  ou `-v`. Temos alguns pontos importantes na utilização dos comandos, com -v quando não existe uma pasta ele cria a pasta, com mount ele da erro.

#### Criando um volume

```console
docker volume create meuvolume
```

#### Usano o volume criado (meuvolume) com o mount

```console
docker run --mount type=volume,source=meuvolume,target=/usr/share/nginx/html nginx
```

#### Compartilhando um volume com volume ` -v `

```console
docker run -v meuvolume:/usr/share/nginx/html nginx
```

#### Compartilhando um diretório com volume ` -v source:target `

```console
docker run -v /Users/cora/Dev/html:/usr/share/nginx/html nginx
```

#### Compartilhando um diretório com mount

```console
docker run --mount type=bind,source=/Users/cora/Dev/html,target=/usr/share/nginx/html nginx
```

#### Listando os volume

```console
docker volume ls
```

#### Verificando detalhes do volume

```console
docker volume inspect meuvolume
```

#### Limpando os volumes de um container

```console
docker volume prune
```

---

### Imagens

#### Criando o Dockerfile

Para criar uma imagem personalizada devemos começar criando um arquivo Dockerfile, ele basicamente é uma receita de bolo para criar sua imagem

```console
FROM nginx:latest

WORKDIR /app

RUN apt-get update && apt-get install vim -y

COPY html/ /usr/share/nginx/html

CMD ["echo", "Hello World"]

ENTRYPOINT ["echo", "Hello World"]

ENV xpto

EXPOSE 80
```

#### Detalhando o Dockerfile

- ` FROM nginx:latest ` cria uma image a partir de uma imagem base
- ` WORKDIR /app ` criar uma pasta dentro do container e quando entramos no bash do container seremos direcionado para esta pasta
- ` RUN apt-get update && apt-get install vim -y ` executa um comando para atualizar os pacotes e instala o vim
- ` COPY html/ /usr/share/nginx/html ` copia um diretório para dentro do container
- ` CMD ["echo", "Hello World"] ` executa um comando no container, o comando pode ser substituido em tempo de execução (no docker run)
- ` CMD ENTRYPOINT ["echo", "Hello World"] ` é um comando fixo, diferente do CMD que é variável
- ` ENV xpto ` atribui um valor para a variável de ambiente
- ` EXPOSE 80 ` expoe a porta 80 do container

> #### Para uma melhor legibilidade podemos quebrar o comando run com uma barra  `\`

```console
RUN apt-get update && \
 apt-get install vim -y
```

#### Para realizar o build da imagem devemos executar o comando no mesmo diretório do Dockerfile, nele informamos o usuário do docker hub, nome da imagem e a versão

```console
docker build -t arnaudsa/nginx-com-vim:latest .
```

#### Listando imagens

```console
docker images 
```

#### Removendo uma imagem

```console
docker rmi nginx
```

#### Removendo todas as imagens

```console
docker image prune -a
```

#### Publicando a imagem no docker hub

```console
docker login

docker push arnaudsa/nginx-com-vim:latest
```

- ` docker login ` faz o login do docker hub
- ` docker push arnaudsa/nginx-com-vim:latest ` envia a imagem para o docker hub

---

### Otimizando imagens

Sempre que vamos trabalhar com imagens é interessante termos uma imagem enxuta, com isso temos mais segurança pois quanto menos pacotes instalados mais seguros estamos, outro ponto interessante e a performance pois com uma imagem menor vou conseguir iniciar minha aplicação mais rapidamente.

Quem nos possibilita essa otimização das imagens é o **Multistage Building,** ele permite fazer o processo de build em 2 ou mais etapas, abaixo temos um exemplo de um Dockerfile para um aplicativo GO, nossa imagem padrão tinha 300MB e conseguimos reduzir o tamanho dela para 5MB.

```console
FROM golang:1.19.2-alpine3.16 as builder

WORKDIR /usr/src/app
COPY go.mod ./
RUN go mod download && go mod verify
COPY . .
RUN go build -v -o /usr/local/bin/app ./...


FROM alpine:3.16
WORKDIR /usr/local/bin/
COPY --from=builder /usr/local/bin/app .
ENTRYPOINT ["./app"]
```

---

### Networks

O principal objetivo e fazer um container se conectar com outro, existem 5 tipos de networks.

- Bridge(ponte) - Quando cria uma network no docker o default é bridge
- Host - Container compartilha a mesma network do docker host
- Overlay - Vários containers em computadores e máquinas diferentes forma uma rede
- Maclan
- None

#### Criando um network

```console
docker network create --driver bridge minharede
```

#### Listando as redes

```console
docker network ls
```

#### Utilizando a rede criada

```console
docker run -d -it --name ubuntu1 --network minharede bash

docker run -d -it --name ubuntu2 --network minharede bash
```

#### Conectando um container em uma rede

```console
docker network connect minharede ubuntu3
```

#### Limpando as redes

```console
docker network prune
```

#### Verificando os detalhes da rede

```console
docker network inspect minharede
```

#### Verificando outros comandos de rede

```console
docker network
```

---

### Compose

#### Exemplo docker-compose.yaml

Quando precisamos trabalhar com vários containers fica inviável termos que ficar digitando vários comandos para inicializar os containers, o docker-compose chegou para resolver esse problema, com ele podemos descrever todos os containers que teremos na aplicação, e a partir de um único comando subir todos os containers.

```console
version: '3.9'

networks:
  gatonet:
    driver: bridge
    name: gatonet

volumes:
  postgres-data:
    name: postgres-data 

services:
  app:
    build:
      context: ./
    image: arnaudsa/sample-app-dev
    container_name: app
    ports:
      - "3000:3000"
    networks: 
      - gatonet
    depends_on:
      - db
  nginx:
    image: nginx:1.23.0
    container_name: nginx
    tty: true
    restart: always
    networks:
      - gatonet
    ports:
      - 8080:80  
    volumes:
      - ./nginx:/etc/nginx/conf.d
    depends_on:
      - app  
  db:
    image: postgres:13-alpine
    container_name: db
    restart: always
    tty: true
    networks:
      - gatonet
    volumes:
      - postgres-data:/var/lib/postgresql/data/pgdata
    environment:
      - POSTGRES_DB=sampledb
      - POSTGRES_USER=test
      - POSTGRES_PASSWORD=test
      - PGDATA=/var/lib/postgresql/data/pgdata
    ports:
      - 5432:5432
  adminer:
    image: adminer
    container_name: adminer
    restart: always
    networks:
      - gatonet
    depends_on:
      - db
    ports:
      - 8181:8080

```

### Iniciando e fazendo o build dos containers

```console
docker-compose up -d --build
```

### Iniciando os containers

```console
docker-compose up -d
```

### Listando os containers ativos

```console
docker-compose ps
```

### Dependência entre containers

Existem situações em que um container tem dependência de outro container, exemplo o container do app depende do container de banco de dados, se o container do app inicializar antes do container de banco de dados teremos uma falha na inicialização do container, para resolver esses problemas podemos utilizar o [dokerize](https://github.com/jwilder/dockerize)

docker-compose.yaml

```console
version: '3'

networks:
  gatonet:
    driver: bridge
    name: gatonet

volumes:
  postgres-data:
    name: postgres-data

services:
  app:
    build:
      context: ./
    image: arnaudsa/sample-app-dev
    entrypoint: dockerize -wait tcp://db:5432 -timeout 30s node app.js
    container_name: app
    ports:
      - "3000:3000"
    networks: 
      - gatonet
    depends_on:
      - db
  db:
    image: postgres:13-alpine
    container_name: db
    restart: always
    tty: true
    networks:
      - gatonet
    volumes:
      - postgres-data:/var/lib/postgresql/data/pgdata
    environment:
      - POSTGRES_DB=sampledb
      - POSTGRES_USER=test
      - POSTGRES_PASSWORD=test
      - PGDATA=/var/lib/postgresql/data/pgdata
    ports:
      - 5432:5432
```

Dockerfile

```console
FROM node:18.10-alpine3.15

WORKDIR /app

COPY . .
RUN npm install

RUN apk add --no-cache openssl

ENV DOCKERIZE_VERSION v0.6.1
RUN wget https://github.com/jwilder/dockerize/releases/download/$DOCKERIZE_VERSION/dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && tar -C /usr/local/bin -xzvf dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz \
    && rm dockerize-alpine-linux-amd64-$DOCKERIZE_VERSION.tar.gz

EXPOSE 3000
```

### Colima

#### Como substituir o runtime de Container do Docker pelo Colima

Remove o docker desktop
```console
sudo /Applications/Docker.app/Contents/MacOS/uninstall
```

Instalar o Docker CLI
```console
brew install docker
```

Instalar o Colima
```console
brew install colima
```

Habilita o nerdctl (necessário para usar o containerd)
```console
colima nerdctl install
```

#### Sobrescreve o endereço do Docker Socks

***Bash***

Adicione o export no .bashrc
```console
export DOCKER_HOST="unix://$HOME/.colima/docker.sock"
```

***ZSH***

Adicione o export no .zshrc (ou .zshenv)
```console
export DOCKER_HOST="unix://$HOME/.colima/docker.sock"
```

***FISH***

Adicione o export no config.fish
```console
set -x DOCKER_HOST "unix://$HOME/.colima/docker.sock"
```

> #### Em seguida reinicie o terminal para atualizar as variáveis.

***Iniciar o Colima***
```console
colima start
```

***Testar o Docker CLI***
```console
docker info
```

Caso apareça a mensagem:

```console 
Client: Docker Engine - Community
 Version:    24.0.2
 Context:    default
 Debug Mode: false

Server:
ERROR: Cannot connect to the Docker daemon at unix:///Users/cora/.colima/docker.sock. Is the docker daemon running?
errors pretty printing info
```

Execute:

```console 
colima delete
```

```console 
colima start
```

#### Sobrescrever configurações do Testcontainers

***Bash:***
Adicione o export no .bashrc
```console 
export TESTCONTAINERS_DOCKER_SOCKET_OVERRIDE=/var/run/docker.sock
export DOCKER_HOST="unix://${HOME}/.colima/docker.sock"
```

***ZSH:***
Adicione o export no .zshrc (ou .zshenv)
```console 
export TESTCONTAINERS_DOCKER_SOCKET_OVERRIDE=/var/run/docker.sock
export DOCKER_HOST="unix:/x/${HOME}/.colima/docker.sock"
```

***FISH:***
Adicione o export no config.fish
```console 
set -x TESTCONTAINERS_DOCKER_SOCKET_OVERRIDE /var/run/docker.sock
set -x DOCKER_HOST unix://{$HOME}/.colima/docker.sock
```

> #### Em seguida reinicie o terminal para atualizar as variáveis.
