# Performance
# Memória
## Análise
```
free -h
```
## Limitar a memória
```
docker run \
  -d \
  -p 8080:80 \
  --name nginx200mb \
  --restart=always \
  -m 200mb \
  nginx
  
```

# Processador
## Tipo
```
grep "model" /proc/cpuinfo
```
## Quantidade disponível
```
grep "model name" /proc/cpuinfo | wc -l
```
## Limitar a Quantidade
```
docker run \
  -d \
  -p 8081:80 \
  --name nginxCPU \
  --restart=always \
  -m 200mb \
  --cpuset-cpus 0-1
  nginx

```

# Container
## Status
### Individual
```
docker stats 127
```
### Todos que estão rodando
```
docker stats $(docker ps -q)
```