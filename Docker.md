# Curso de Docker

> ## Menu
>
>
> - [Home](README.md)
> - [Containers](#containers)
> - [Volumes](#volumes)
> - [Imagens](#imagens)
> - [Networks](#networks)

---

### Containers

#### Executando um container

```console
docker run -d -p 8080:80 --name nginx nginx:latest
```

#### Detalhando os parametros

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

docker logs -f --tail 1000 nginx
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

#### Compartilhando um volume com volume ( -v )

```console
docker run -v meuvolume:/usr/share/nginx/html nginx
```

#### Compartilhando um diretório com volume ( -v source:target )

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

> ####  **Importante:** O mapeamento com -v quando colocamos uma pasta que não existe ele cria a pasta automaticamente com mount ele da erro

---

### Imagens

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

#### Detalhando o Dockerfile

- [ FROM nginx:latest ] cria uma image a partir de uma imagem base
- [ WORKDIR /app ] criar uma pasta dentro do container e quando entramos no bash do container seremos direcionado para esta pasta
- [ RUN apt-get update && apt-get install vim -y ] executa um comando para atualizar os pacotes e instala o vim
- [ COPY html/ /usr/share/nginx/html ] copia um diretório para dentro do container
- [ CMD ["echo", "Hello World"] ] executa um comando no container, o comando pode ser substituido em tempo de execução (no docker run)
- [ CMD ENTRYPOINT ["echo", "Hello World"] ] é um comando fixo, diferente do CMD que é variável
- [ ENV xpto ] atribui um valor para a variável de ambiente
- [ EXPOSE 80 ] expoe a porta 80 do container

> ### Para uma melhor legibilidade podemos quebrar o comando run com uma barra ( \ )

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

- [ docker login ] faz o login do docker hub
- [ docker push arnaudsa/nginx-com-vim:latest ] envia a imagem para o docker hub

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
