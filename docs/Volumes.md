# Volumes
Ao criar um caontainer o docker cria um volume de nome aleatório<br>

# Onde estão meus dados
O item Mounts.Source indica a pasta que os dados estão<br>
```
docker inspect <id>
```
Para facilitar poderá mostrar só o item que deseja<br>
```
docker inspect -f '{{json .Mounts}}' <id>
```

Listar volumes:<br>
```
docker volume ls
```
Listar volumes pendentes:<br>
```
docker volume ls -f dangling=true
```
Remover volumes pendentes:<br>

```
docker volume prune
docker volume ls -f dangling=true
```
# Trabalhando com volumes
## Criar
Vamos criar um volume<br>
```
docker volume ls
docker volume create mysql.5.7
```

## Atribuir
Agora iremos atribuir o volume ao container<br>
```
docker run \
    -d \
    -p 3306:3306 \
    -e MYSQL_ROOT_PASSWORD=my-password \
    -e MYSQL_USER=root \
    -e MYSQL_PASSWORD=my-password \
    -v mysql.5.7:/var/lib/mysql \
    --network mysql \
    --name mysql.5.7 \
    mysql:5.7
```

ad8

## Inspecionar
```
docker volume inspect mysql.5.7
```
Term como saída:<br>
```
[
    {
        "CreatedAt": "2020-06-28T15:28:43-03:00",
        "Driver": "local",
        "Labels": {},
        "Mountpoint": "/var/lib/docker/volumes/mysql.5.7/_data",
        "Name": "mysql.5.7",
        "Options": {},
        "Scope": "local"
    }
]
```
## Acessar os dados
Para acessar os dados do container basta acessar o Mountpoint como su<br>
```
su
cd /var/lib/docker/volumes/mysql.5.7/_data
```
Para fazer backup dos dados utiliza o comando docker cp, pois o linux trava a pasta e alguns arquivos.<br>
Testar:<br>
```
ls -la
sudo chown 1000:1000 -R </nome/da/pasta/> -R
ls -la
```
