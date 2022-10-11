
> ## Menu
> - [Home](README.md)
> - [Execução de container](#execucao)
> - [Comandos Gerenciais](#gerencial)
> - [Criando uma imagem](#imagem)

# Execução de container <a name="execucao"></a>

#### Executar um container
```console
docker run ubuntu
```

#### Modo iterativo (-it) para acessar o bash do container
```console
docker run -it ubuntu:latest bash
```

#### Não atacha a tela
```console
docker run -d ubuntu
```

#### Define um nome para o container
```console
docker run --name ubu ubuntu
```

#### Expondo uma porta para acesso
```console
docker run -p 8080:80 nginx
```

#### Entrando no modo iterativo em um container em execução com o nome nginx no bash do container
```console
docker exec -it nginx bash
```

#### Compartilhando um volume
```console
docker run -v /Users/cora/Dev/html:/usr/share/nginx/html nginx
```

#### Compartilhando um volume com mount
```console
docker run --mount type=bind,source=/Users/cora/Dev/html,target=/usr/share/nginx/html nginx
```

#### Criando um volume
```console
docker volume create meuvolume
```

#### Usano o volume criado "meuvolume" com o mount
```console
docker run --mount type=volume,source=meuvolume,target=/usr/share/nginx/html nginx
```

#### Executando um container com todos os parâmetros acima
```console
docker run -d -p 8080:80 --name nginx -mounts type=bind,source=/Users/cora/Dev/html,target=/usr/share/nginx/html nginx
```

>  ### O mapeamento com -v quando colocamos uma pasta que não existe ele vai criar uma automaticamente com mount isso não ocorre ele da erro

<br/><br/>

# Comandos Gerenciais <a name="gerencial"></a>
#### Limpando os volumes de um container
```console
docker volume prune
```

#### Listando os volume
```console
docker volume ls
```

##### Verificando detalhes do volume
```console
docker volume inspect meuvolume
```

#### Executando um comando em um container para listar os arquivos
```console
docker exec nginx ls
```

#### Listando os processos docker
```console
docker ps
```

#### Listando os processos suspensos
```console
docker ps -a
```

#### Parando um container
```console
docker stop 2222222
```
#### Removendo um container
```console
docker rm 2222222
```

#### Forçando a remoção do container
```console
docker rm 2222222 -f
```

#### Removendo todos os containers ativos e inativos
```console
docker rm $(docker ps -aq) -f
```
#### Listando imagens
```console
docker images 
```
#### Removendo uma imagem
```console
docker rmi nginx
```

#### Verificando os logs
```console
docker log container_name

docker logs -f --tail 1000 sample-app
```

<br/><br/>

# Criando uma imagem <a name="imagem"></a>

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

#### Workdir diretório que você vai trabalhar dentro do container, quando você começa a trabalhar no container ele vai criar uma pasta 
e quando você entrar no container será direcionado para esta pasta
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

# Trabalhando com redes (networks) <a name="networks"></a>
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

####  Listando as redes
```console
docker network ls
```

####  Utilizando a rede criada
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
docker network inspect bridge
```

#### Verificando outros comandos de rede
```console
docker network
```
