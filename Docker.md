# Execução de container

#### executar um container
```console
docker run ubuntu
```

#### modo iterativo (-it) para acessar o bash do container
```console
docker run -it ubuntu:latest bash
```

#### não atacha a tela
```console
docker run -d ubuntu
```

#### executar um container e definir um nome
```console
docker run --name ubu ubuntu
```

#### executar um container expondo uma porta para acesso
```console
docker run -p 8080:80 nginx
```

#### Executando um comando em um container e entrando no modo iterativo no bash do container
```console
docker exec -it nginx bash
```

#### executar um container compartilhando um volume
```console
docker run -v /Users/cora/Dev/html:/usr/share/nginx/html nginx
```

#### executar um container compartilhando um volume com mount
```console
docker run --mount type=bind,source=/Users/cora/Dev/html,target=/usr/share/nginx/html nginx
```

#### criando um volume
```console
docker volume create meuvolume
```

#### usano o volume criado "meuvolume" com o mount
```console
docker run --mount type=volume,source=meuvolume,target=/usr/share/nginx/html nginx
```

#### Comando full com todos parâmetros acima
```console
docker run -d -p 8080:80 --name nginx -mounts type=bind,source=/Users/cora/Dev/html,target=/usr/share/nginx/html nginx
```

>  O mapeamento com -v quando colocamos uma pasta que não existe ele vai criar uma automaticamente com mount isso não ocorre ele da erro

<br/><br/>

# Comandos Gerenciais
#### limpando os volumes de um container
```console
docker volume prune
```

#### listando os volume
```console
docker volume ls
```

##### verificando detalhes do volume
```console
docker volume inspect meuvolume
```

#### Executando um comando em um container para listar os arquivos
```console
docker exec nginx ls
```

#### listando os processos docker
```console
docker ps
```

#### listando os processos suspensos
```console
docker ps -a
```

#### parando um container
```console
docker stop 2222222
```
#### removendo um container
```console
docker rm 2222222
```

#### forçando a remoção do container
```console
docker rm 2222222 -f
```

#### removendo todos os containers ativos e inativos
```console
docker rm $(docker ps -aq) -f
```
#### listando imagens
```console
docker images 
```
#### removendo uma imagem
```console
docker rmi nginx
```

# Criando uma imagem

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
#### Executa um comando para atualizar meus pacotes e instala o vim
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


#### Realizando o build da imagem
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