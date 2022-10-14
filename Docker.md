# Curso de Docker

> ## Menu
>
>
> - [Home](README.md)
> - [Containers](#containers)
> - [Volumes](#volumes)
> - [Trabalhando com imagens](#trabalhando-com-imagens)
> - [Networks](#networks)

---

### Containers

#### Executando um container

```console
docker run -d -p 8080:80 --name nginx nginx:latest
```

##### Detalhando os parametros

- [ -d ] executa o container sem atachar o console
- [ -p 8080:80 ] aqui estamos mapeando a porta 8080 do host com a porta 80 do container, com isso conseguimos acessar o nginx através do nosso host.
- [ --name nginx ] aqui definimos um nome para o container, isso facilita o gerenciamento do container
  
#### Executando um container em modo iterativo (-it) e acessando o bash

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

docker logs -f --tail 1000 sample-app
```

#### Listando os arquivos em um container

```console
docker exec nginx ls
```

---

### Volumes

#### Criando um volume

```console
docker volume create meuvolume
```

#### Usano o volume criado (meuvolume) com o mount

```console
docker run --mount type=volume,source=meuvolume,target=/usr/share/nginx/html nginx
```

### Compartilhando um volume com volume ( -v )

```console
docker run -v meuvolume:/usr/share/nginx/html nginx
```

### Compartilhando um diretório com volume ( -v source:target )

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

##### Verificando detalhes do volume

```console
docker volume inspect meuvolume
```

#### Limpando os volumes de um container

```console
docker volume prune
```

>#### O mapeamento com -v quando colocamos uma pasta que não existe ele vai criar uma automaticamente com mount isso não ocorre ele da erro

---

## Gerenciando container

### Listando imagens

```console
docker images 
```

#### Removendo uma imagem

```console
docker rmi nginx
```

---

## Trabalhando com imagens

#### Para criar uma imagem personalizada devemos começar criando um arquivo Dockerfile, ele basicamente é uma receita de bolo para criar uma imagem

Exemplo de Dockerfile

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

### Descrevendo comandos do Dockerfile

#### Cria uma imagem a partir de uma imagem base

```console
FROM <image name>
```

#### Executa um comando para atualizar os pacotes e instala o vim

```console
RUN apt-get update && apt-get install vim -y
```

#### Quebrando o comando run com \

```console
RUN apt-get update && \
 apt-get install vim -y
```

#### Workdir diretório que você vai trabalhar dentro do container

Quando você começa a trabalhar no container ele vai criar uma pasta,
e quando você entrar no container será direcionado para a pasta criada

```console
WORKDIR /app
```

#### Copia uma arquivo que está dentro do computador para dentro do container

```console
COPY html/ /usr/share/nginx/html
```

#### Executa um comando no container, o comando pode ser substituido em tempo de execução (no docker run)

```console
CMD ["echo", "Hello World"]
```

#### É um comando fixo, diferente do CMD que é variável

```console
ENTRYPOINT ["echo", "Hello"]
```

#### Adiciona variável de ambiente

```console
ENV xpto
```

#### Expondo a porta 80

```console
EXPOSE 80
```

#### Faz o build da imagem

```console
docker build -t arnaudsa/nginx-com-vim:latest .
```

### Publicando a imagem no docker hub

#### Fazer login no docker hub

```console
docker login
```

#### Fazendo o push da imagem

```console
docker push arnaudsa/nginx-com-vim:latest
```

---

## Networks

O principal objetivo e fazer um container se conectar com outro, existem 5 tipos de networks.

- Bridge(ponte) - Quando cria uma network no docker o default é bridge
- Host - Container compartilha a mesma network do docker host
- Overlay - Vários containers em computadores e máquinas diferentes forma uma rede
- Maclan
- None

#### Criando uma rede

```console
docker network create --driver bridge minharede
```

#### Listando as redes

```console
docker network ls
```

#### Utilizando a rede criada

```console
docker run -dit --name ubuntu1 --network minharede bash

docker run -dit --name ubuntu2 --network minharede bash
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
