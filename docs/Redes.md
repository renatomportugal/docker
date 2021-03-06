# Redes
# Criando uma rede
```
docker network create \
  -d \
  bridge \
  --subnet 172.18.0.0/16 \
  --gateway 172.18.0.1 \
  bridge.172.18
```

# Conectando duas redes
## Crie a primeira rede
```
docker network create \
  -d \
  bridge \
  --subnet 172.30.0.0/16 \
  --gateway 172.30.0.1 \
  bridge.172.30
```

## Crie a segunda rede
```
docker network create \
  -d \
  bridge \
  --subnet 172.31.0.0/16 \
  --gateway 172.31.0.1 \
  bridge.172.31
```

## Crie um container na primeira rede
```
docker run \
    -d \
    -p 3306:3306 \
    -e MYSQL_ROOT_PASSWORD=my-password \
    -e MYSQL_USER=root \
    -e MYSQL_PASSWORD=my-password \
    --network bridge.172.30 \
    --name mysql.5.7 \
    mysql:5.7
```


## Crie um container na segunda rede
```
docker run \
    -d \
    -p 8080:80 \
    -e PMA_HOST=mysql.5.7 \
    --network bridge.172.31 \
    --name phpmyadmin \
    phpmyadmin/phpmyadmin
```
## Conectar os dois containers
Na verdade a conexão é a inclusão do container desejado na rede que o container alvo está. Consequentemente ele consegue acessar todos os outros containers.<br>
```
docker network container connect net.172.30 phpmyadmin
```

## Desconectando um container de uma rede
```
docker network container disconnect net.172.30 phpmyadmin
```

# Atribuindo IP estático
## Criar a rede
```
docker network create \
  -d \
  bridge \
  --subnet 172.40.0.0/16 \
  --gateway 172.40.0.1 \
  net.172.40
```

## Criar um container dentro da rede com o IP
```
docker run \
  -d \
  -p 8080:80 \
  --name mginx \
  --network bridge.172.40 \
  --ip 172.40.0.100 \
  nginx
```

## Criar um container isolado
```
docker run \
  -d \
  --name mginx.isolated \
  --network none \
  nginx
```

# Organizando os Containers
## Fazendo a rede
```
docker network create \
  -d \
  bridge \
  --subnet 66.6.0.0/16 \
  --gateway 66.6.0.1 \
  bridge.66.6
```

## Criando os Containers de banco de dados

### MySQL
Criar o volume<br>
```
docker volume create mysql.5.7.var.lib.mysql
```
```
docker run \
    -d \
    -p 3306:3306 \
    -e MYSQL_ROOT_PASSWORD=my-password \
    -e MYSQL_USER=root \
    -e MYSQL_PASSWORD=my-password \
    -v mysql.5.7.var.lib.mysql:/var/lib/mysql \
    --network bridge.66.6 \
    --name mysql.5.7 \
    --ip 66.6.0.5 \
    --restart=always \
    mysql:5.7
```
d8e

## Aplicações
### 8080 - PhpMyAdmin
```
docker run \
    -d \
    -p 8080:80 \
    -e PMA_HOST=mysql.5.7 \
    --network bridge.66.6 \
    --name phpmyadmin \
    --ip 66.6.0.15 \
    --restart=always \
    phpmyadmin/phpmyadmin
```

### 8000 - Owncloud
Criar o volume<br>
```
docker volume create owncloud.var.www.html.data
```
```
docker run \
  -d \
  -p 8000:80 \
  -v owncloud.var.www.html.data:/var/www/html/data \
  --network bridge.66.6 \
  --name owncloud \
  --ip 66.6.0.100 \
  --restart=always \
owncloud
```
64f

```
docker run -it busybox
```

Abra outra janela<br>
```
docker run -it busybox
```
Verifique os números IP dos containers em cada janela<br>
```
hostname -i
hostname
```
172.17.0.2<br>
135774e941a8<br>

172.17.0.3<br>
135774e941a8o<br>

Para que um container reconheça outro pelo Aliases<br>
hostname é interno, name é externo<br>

Abra o terminal id 13<br>
```
exit
```
```
docker run -it --name busybox1 -h busybox-one busybox
hostname
hostname -i
```
abra o terminal id 06
```
exit
```

```
docker run -it --name busybox2 -h busybox-two busybox
hostname
hostname -i
```

```
docker network ls
```

Copie o id da bridge<br>
0e195bb2ae21<br>

```
docker network inspect bridge
docker network create custom
```
8d<br>

```
docker network inspect custom
```

saia dos dois terminais<br>
```
exit
```

e elimine todas as instâncias<br>
```
docker container prune -f
docker ps -a
```

abra dois terminais<br>
```
docker run -it --network custom busybox
hostname -i
hostname
```

172.18.0.2<br>
317653a37985<br>

172.18.0.3<br>
36b6a47edc2e<br>

Agora faremos um ping do IP final 2 para o final 3, por IP e por hostname<br>
```
ping 172.18.0.3
ping 36b6a47edc2e
```

Conclusão: Containers na rede custom se comunicam com hostnames.<br>
e não conseguem se comunicar com os nomes dinâmicos de hosts.<br>

Using custom persistent names for connectivity in the custom network<br>
saia dos dois terminais<br>
```
exit
```

```
docker run -it --network custom --name busybox1 busybox
docker run -it --network custom --name busybox2 busybox
docker ps
```

na janela do busybox1<br>
```
ping busybox2
ping 0e22d7f144a0
```

```
hostname -i
hostname
```

172.18.0.2<br>
5a91c70cbed6<br>

172.18.0.3<br>
0e22d7f144a0<br>

```
docker network inspect custom
docker inspect busybox1
docker inspect busybox2
```

na janela do busybox1<br>
```
exit
docker ps
docker ps -a
```

```
docker start -i busybox1
ping busybox2
```

na janela do busybox2<br>
```
ping busybox1
```

saia dos dois terminais<br>
```
exit
```
