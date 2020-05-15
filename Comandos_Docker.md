# CONCEITOS BÁSICOS

Executar um conteiner sem cria-lo
docker container run —rm ubuntu bash

Criar um container com nome
docker container run —name my app ubuntu bash

Mapeamento de porta
docker container run -p 8080:80  nginx 

Mapeamento de volume
docker container run -p 8080:80 -v $(pwd)/html:/usr/share/nginx/html nginx

Executar o container em background
docker container -d —name ex-daemon-basic -p 8080:80 -v $(pwd)/html:/usr/share/nginx/html nginx

Flags utilizadas
- -d (daemon)
- -p (porta)
- -n (nomear o container)
- -v (volume)
- ps (lista os container em execução) docker container ls
- ps -a (lista todos os container) docker container ls -a
- exec
- rm
- -t (tag) cria um ponteiro para uma determinada versão. Evite apontar para a versão latest
- run (executa o container) 
- --net + tipo_de_rede 

Parando container
docker container stop (número do container) ou (nome do container)

Iniciando o container
docker container start (número do container) ou (nome do container) 

Acessar os logs do container
docker container logs (nome do container) ou (número do container)

Inspecionando a estrutura do container
docker container inspect (nome do container) ou (número do container)

Executar comandos dentro do container (exec)
docker container exec (nome do container) ou (número do container) uname -or

Listar container / imagens e volumes
docker container ls
docker image ls
docker volume ls

Apagando container ou imagens
docker container   rm (nome do container) ou (número do container)
docker image rm (nome do container) ou (número do container)

Criando uma TAG de uma imagem ou ponteiro para uma mesma hashtag
docker image tag nginx:latest nova-nginx

GERENCIAMENTO

Baixando imagens do Docker Hub
docker image pull image

Lista as imagens
docker image ls

Exclui a imagem
docker image rm image

Inspecionar a imagem
docker image inspect image

Cria um ponteiro de uma imagem
docker image tag image

Cria uma imagem com as devidas alterações
docker image build image

Envia a imagem para armazenamento local ou remoto
docker image push image

 CONCEITOS AVANÇADOS

Docker Hub é público.
Docker Registry é privado.

CRIANDO UMA IMAGEM com arquivo Dockerfile
docker image build -t ex-simples  .

CRIANDO UMA IMAGEM COM ARG e ENV com arquivo Dockerfile
docker image build -t ex-simples-args .

Alterando o argumento através do comando build
docker image build —build-arg S3_BUCKET=myapp ex-simples-args .

## Acessando volume de outro container
Para acessar o volume de outro container utilizamos o mapeamento com *--volume-from=nome-da-container*
```
# exemplo de comando completo
docker container rum -it --volume-from=nome-do-container seu-container comando
```

# Dockerfile
#### imagem que será criada
```FROM nginx:latest```
#### Informações do autor
```LABEL maintainer 'Fábio Ribeiro <Fabio Ribeiro at rybeiro@gmail.com>'```
#### Passando argumentos para o container
```ARG S3_BUCKET=files```
#### Passando variáveis de ambiente para o container
```ENV S3_BUCKET=${S3_BUCKET}```
#### executa o comando do unix que cria e imprime no conteudo.html
```RUN echo '<h1>Olá Conteúdo!!</h1>' > /usr/share/nginx/html/conteudo.html```
#### copia conteúdo para o container
```COPY *.html /usr/share/nginx/html/```
#### usuario ativo e logado no sistema
```USER www```
#### cria o volume que poderá ser acessado por outro container
```VOLUME /log```
#### diretorio padrao de trabalho
```WORKDIR /app```
#### expondo a porta
```EXPOSE 8000```
#### Ponto de entrada quando o container for iniciado
```ENTRYPOINT ["/usr/local/bin/python"]```
#### comando que será executado logo após 
```CMD ["run.py"]```

# Fazendo o push de uma imagem que nós criamos para o hub.docker.com
```
# Criando a imagem personalizada
docker image tag nome-imagem user-docker-hub/nome-da-nova-imagem:1.0
# Efetuando o login
docker login --username=user-docker-hub
# Entre com a senha: ***
*output:* Login Succeeded
# Fazendo o push
docker image push user-docker-hub/nome-da-nova-imagem:1.0
```

# Tipos de redes Docker
# None
Container isolado sem comunicação interna ou externa
```
# Container padrão
docker container run --rm alpine ash -c "ifconfig"
# Container tipo none
docker container run --rm --net none alpine ash -c "ifconfig"
```
# Bridge Network
Ponte entre os containers e o host com uma camada (bridge)
```
# Container 1
docker container run -d --name container1 alpine sleep 1000
# Container 2
docker container run -d --name container2 alpine sleep 1000
# Pegando o IP do conteiner2
docker exec -it container2 ifconfig
# # testando a comunicação bridge entre os containers com ping
docker exec -it container1 ping 172.70.0.3 (IP_descoberto)
```
## Criando redes personalizadas
```
docker network create --driver bridge nome-da-nova-rede
```
## listando as redes
```
docker networks ls
```
## Informações da nova rede (subnet e gateway)
```
docker network inspect nome-da-nova-rede
```
## Executando o container e setando a nova rede
```
docker container run -d --name container3 --net nome-da-nova-rede alpine sleep 1000
# Informações da interface de rede
docker exec -it container3 ifconfig
# Teste com ping
docker exec -it container3 ping 172.70.0.3 (IP_descoberto)
*output:* Sem retorno
```
## Conectando e desconectando com redes distintas
```
# Connect
docker network connect bridge container3
docker exec -it container3 ping 172.70.0.3 (IP_descoberto)
# Disconnect
docker network disconnect bridge container3
docker exec -it container3 ping 172.70.0.3 (IP_descoberto)
```

# Host Network
Nesse tipo o Container utiliza-se diretamente da interface de rede do host (nível de proteção mais baixo) porém a comunicação é mais rápida.
```
docker container run --name container4 --net host alpine sleep 1000
# Informações da interface utilizada pelo container
docker container exec -it container4 ifconfig
```

# Overlay Network (swarm) 
Utilizada para formação de cluster

# Gerenciando microserviços

