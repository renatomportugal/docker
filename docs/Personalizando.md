# Personalizando
# Personalizar busca
A personalização do comando docker ps é simples, basta criar um arquivo na pasta pessoal.<br>
```
cd ~
touch .bashrc
gedit .bashrc
```
Atenção: não atribua os parâmetros reservados que podem ser vistos em docker ps --help: (-a, -f, -n, -l, -q, -s)<br>

## docker ps -t
```
function docker () {
    if [[ "$@" == "ps -t" ]]; then
        command docker ps --all --format "{{.ID}}\t{{.Names}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}" \
            | (echo -e "CONTAINER_ID\tNAMES\tIMAGE\tPORTS\tSTATUS" && cat) \
            | awk '{printf "\033[1;32m%s\t\033[01;38;5;95;38;5;196m%s\t\033[00m\033[1;34m%s\t\033[01;90m%s %s %s %s %s %s %s\033[00m\n", $1, $2, $3, $4, $5, $6, $7, $8, $9, $10;}' \
            | column -s$'\t' -t \
            | awk 'NR<2{print $0;next}{print $0 | "sort --key=2"}'
    else
        command docker "$@"
    fi
}
```
SAÍDA (colunas são coloridas, não foi possível demostrar aqui):<br>
```
CONTAINER_ID   NAMES   IMAGE   PORTS STATUS     
2d25aba85ede   admiring_newton   nextcloud   Exited (0) 24 hours ago  
3b0ea005351c   App01   php:7.1.26-apache   Exited (0) 3 days ago  
25714f25773e   bitnami-moodle_mariadb_1   bitnami/mariadb:10.3-debian-10   Exited (0) 2 days ago  
1c7862ffa880   bitnami-moodle_moodle_1   bitnami/moodle:3-debian-10   Exited (0) 2 days ago  
e99eaeff8999   drupal   drupal   Exited (0) 4 days ago  
4881e604353c   elated_neumann   nextcloud   Exited (0) 24 hours ago  
895f576ff2f1   gifted_lehmann   owncloud   Exited (0) 23 hours ago  
1bb866edd983   jenkins   jenkins/jenkins   Exited (143) 5 hours ago  
2001f6e0be92   joomla22JUN   joomla   Exited (0) 4 days ago  
4da9bb2dd5aa   ls-server   lightstreamer   0.0.0.0:80->8080/tcp Up 3 minutes   
81c7fd9fa6ca   lucid_antonelli   phpmyadmin/phpmyadmin   Exited (0) 2 days ago  
9ab45431bace   lucid_sammet   plone   Exited (137) 4 days ago  
49f30d9f7a9c   mediawiki   mediawiki   Exited (0) 4 days ago  
d3195e6bd55a   mongo   mongo   Exited (0) 5 hours ago  
416df0ec470d   mysql.5.7   mysql:5.7   Exited (0) 23 hours ago  
885a02020ee0   mysql.5.7.External   mysql:5.7   Exited (0) 24 hours ago  
20829996bc54   mysql.5.7.wp   mysql:5.7   Exited (0) 24 hours ago  
12725c7f68f9   nginx   nginx   Exited (0) 5 hours ago  
14cebbf8241c   nginx200mb   nginx   0.0.0.0:8080->80/tcp Up 3 minutes   
c6d1833148f4   nginxCPU   nginx   0.0.0.0:8081->80/tcp Up 3 minutes   
44c80911220f   nuxeo   nuxeo   Exited (137) 4 days ago  
c81e8baea872   phpmyadmin   phpmyadmin/phpmyadmin   Exited (0) 31 hours ago  
ab9317a045ca   postgres   postgres:10   Exited (0) 5 hours ago  
79c1b49de655   rancher   rancher/server   Exited (0) 23 hours ago  
14e3f088da00   redmine   redmine   Exited (1) 4 days ago  
46970f25b93c   trusting_tu   adminer   Exited (0) 4 days ago  

```

## docker ps -u
```
function docker () {
    if [[ "$@" == "ps -u" ]]; then
        command docker ps -a | perl -ne '@cols = split /\s{2,}/, $_; printf "%30s %20s %30s\n", $cols[1], $cols[3], $cols[4]'        
    else
        command docker "$@"
    fi
}
```
SAÍDA:<br>
```
                        IMAGE              CREATED                         STATUS
                         nginx          3 hours ago         Exited (0) 3 hours ago
                         nginx          3 hours ago         Exited (0) 3 hours ago
                         nginx          4 hours ago         Exited (0) 3 hours ago
               jenkins/jenkins          4 hours ago       Exited (143) 3 hours ago
                   postgres:10          4 hours ago         Exited (0) 3 hours ago
                         mongo         18 hours ago         Exited (0) 3 hours ago
                rancher/server         21 hours ago        Exited (0) 21 hours ago
         phpmyadmin/phpmyadmin           2 days ago        Exited (0) 29 hours ago
                     mysql:5.7           2 days ago        Exited (0) 21 hours ago
    bitnami/moodle:3-debian-10           2 days ago          Exited (0) 2 days ago
bitnami/mariadb:10.3-debian-10           2 days ago          Exited (0) 2 days ago
```

## docker ps -v
```
function docker () {
    if [[ "$@" == "ps -v" ]]; then
        command docker ps -a --format "table {{.ID}}\t{{.Names}}\t{{.Ports}}" | (read -r; printf "%s\n" "$REPLY"; sort -k 3 )        
    else
        command docker "$@"
    fi
}
```
SAÍDA:<br>
```
CONTAINER ID        NAMES                      PORTS
25714f25773e        bitnami-moodle_mariadb_1   
1c7862ffa880        bitnami-moodle_moodle_1    
885a02020ee0        mysql.5.7.External         
2d25aba85ede        admiring_newton            
81c7fd9fa6ca        lucid_antonelli            
4881e604353c        elated_neumann             
895f576ff2f1        gifted_lehmann             
20829996bc54        mysql.5.7.wp               
9ab45431bace        lucid_sammet               
2001f6e0be92        joomla22JUN                
46970f25b93c        trusting_tu                
c81e8baea872        phpmyadmin                 
416df0ec470d        mysql.5.7                  
49f30d9f7a9c        mediawiki                  
ab9317a045ca        postgres                   
14e3f088da00        redmine                    
1bb866edd983        jenkins                    
79c1b49de655        rancher                    
e99eaeff8999        drupal                     
12725c7f68f9        nginx                      
3b0ea005351c        App01                      
44c80911220f        nuxeo                      
d3195e6bd55a        mongo                      
4da9bb2dd5aa        ls-server                  0.0.0.0:80->8080/tcp
14cebbf8241c        nginx200mb                 0.0.0.0:8080->80/tcp
c6d1833148f4        nginxCPU                   0.0.0.0:8081->80/tcp

```

## docker ps -w
```
function docker () {
    if [[ "$@" == "ps -w" ]]; then
        command docker ps | perl -ple "s/\$/\n\n/g;s/\s{2,}/\n/g;s/(Up)/\\e\[32m\$1\\e\[0m/g;s/(Down)/\\e\[31m\\e\[5m\$1\\e\[25m\\e\[0m/g;s/^([^\n]+)/\\e\[1m\$1\\e\[0m/g;s/(\w+?)$/\\e\[4m\$1\\e\[24m/g" | more
    else
        command docker "$@"
    fi
}
```
SAÍDA:<br>
```
CONTAINER ID
IMAGE
COMMAND
CREATED
STATUS
PORTS
NAMES

c6d1833148f4
nginx
"/docker-entrypoint.…"
11 hours ago
Up 9 minutes
0.0.0.0:8081->80/tcp
nginxCPU

14cebbf8241c
nginx
"/docker-entrypoint.…"
11 hours ago
Up 9 minutes
0.0.0.0:8080->80/tcp
nginx200mb

```

## docker ps -x
```
function docker () {
    if [[ "$@" == "ps -x" ]]; then
        command docker container ls -n 2
    else
        command docker "$@"
    fi
}
```
SAÍDA:<br>
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                  NAMES
c6d1833148f4        nginx               "/docker-entrypoint.…"   11 hours ago        Up 10 minutes       0.0.0.0:8081->80/tcp   nginxCPU
14cebbf8241c        nginx               "/docker-entrypoint.…"   11 hours ago        Up 10 minutes       0.0.0.0:8080->80/tcp   nginx200mb

```

## docker ps -y
```
function docker () {
    if [[ "$@" == "ps -y" ]]; then
        command docker container ls -f "status=exited"
    else
        command docker "$@"
    fi
}
```
SAÍDA:<br>
```
CONTAINER ID        IMAGE                            COMMAND                  CREATED             STATUS                      PORTS               NAMES
12725c7f68f9        nginx                            "/docker-entrypoint.…"   12 hours ago        Exited (0) 11 hours ago                         nginx
1bb866edd983        jenkins/jenkins                  "/sbin/tini -- /usr/…"   12 hours ago        Exited (143) 11 hours ago                       jenkins
ab9317a045ca        postgres:10                      "docker-entrypoint.s…"   12 hours ago        Exited (0) 11 hours ago                         postgres
d3195e6bd55a        mongo                            "docker-entrypoint.s…"   26 hours ago        Exited (0) 11 hours ago                         mongo
79c1b49de655        rancher/server                   "/usr/bin/entry /usr…"   29 hours ago        Exited (0) 29 hours ago                         rancher
c81e8baea872        phpmyadmin/phpmyadmin            "/docker-entrypoint.…"   2 days ago          Exited (0) 37 hours ago                         phpmyadmin
416df0ec470d        mysql:5.7                        "docker-entrypoint.s…"   2 days ago          Exited (0) 29 hours ago                         mysql.5.7

```
