#Configuração de um cluster com 2 nós do RabbitMq

**1- (rabbitmq.repo) deve ser adicionado ao /etc/yum.repos.d/diretório.**

**2- copy to (rabbitmq.repo)**

```
[modern-erlang]
name=modern-erlang-el8
# Use a set of mirrors maintained by the RabbitMQ core team.
# The mirrors have significantly higher bandwidth quotas.
baseurl=https://yum1.rabbitmq.com/erlang/el/8/$basearch
        https://yum2.rabbitmq.com/erlang/el/8/$basearch
repo_gpgcheck=1
enabled=1
gpgkey=https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-erlang.E495BB49CC4BBE5B.key
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[modern-erlang-noarch]
name=modern-erlang-el8-noarch
# Use a set of mirrors maintained by the RabbitMQ core team.
# The mirrors have significantly higher bandwidth quotas.
baseurl=https://yum1.rabbitmq.com/erlang/el/8/noarch
        https://yum2.rabbitmq.com/erlang/el/8/noarch
repo_gpgcheck=1
enabled=1
gpgkey=https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-erlang.E495BB49CC4BBE5B.key
       https://github.com/rabbitmq/signing-keys/releases/download/3.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[modern-erlang-source]
name=modern-erlang-el8-source
# Use a set of mirrors maintained by the RabbitMQ core team.
# The mirrors have significantly higher bandwidth quotas.
baseurl=https://yum1.rabbitmq.com/erlang/el/8/SRPMS
        https://yum2.rabbitmq.com/erlang/el/8/SRPMS
repo_gpgcheck=1
enabled=1
gpgkey=https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-erlang.E495BB49CC4BBE5B.key
       https://github.com/rabbitmq/signing-keys/releases/download/3.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1


##
## RabbitMQ Server
##

[rabbitmq-el8]
name=rabbitmq-el8
baseurl=https://yum2.rabbitmq.com/rabbitmq/el/8/$basearch
        https://yum1.rabbitmq.com/rabbitmq/el/8/$basearch
repo_gpgcheck=1
enabled=1
# Cloudsmith's repository key and RabbitMQ package signing key
gpgkey=https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-server.9F4587F226208342.key
       https://github.com/rabbitmq/signing-keys/releases/download/3.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[rabbitmq-el8-noarch]
name=rabbitmq-el8-noarch
baseurl=https://yum2.rabbitmq.com/rabbitmq/el/8/noarch
        https://yum1.rabbitmq.com/rabbitmq/el/8/noarch
repo_gpgcheck=1
enabled=1
# Cloudsmith's repository key and RabbitMQ package signing key
gpgkey=https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-server.9F4587F226208342.key
       https://github.com/rabbitmq/signing-keys/releases/download/3.0/rabbitmq-release-signing-key.asc
gpgcheck=1
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md

[rabbitmq-el8-source]
name=rabbitmq-el8-source
baseurl=https://yum2.rabbitmq.com/rabbitmq/el/8/SRPMS
        https://yum1.rabbitmq.com/rabbitmq/el/8/SRPMS
repo_gpgcheck=1
enabled=1
gpgkey=https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-server.9F4587F226208342.key
gpgcheck=0
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
pkg_gpgcheck=1
autorefresh=1
type=rpm-md
```

***3- Instalar pacotes com dnf (yum)*** 

```
Atualizar metadados do pacote:
dnf update -y

Em seguida, instale as dependências dos repositórios padrão:
dnf install -y logrotate

Por fim, instale o Erlang e o RabbitMQ modernos:
dnf install -y erlang rabbitmq-server
```

***4- Inicialização***

```
habilitando no inicio da sistema:
systemctl enable rabbitmq-server

systemctl start rabbitmq-server

systemctl status  rabbitmq-server
```

***5- liberações do firewall***

```
sudo firewall-cmd --zone=public --add-port=1883/tcp --permanent
sudo firewall-cmd --zone=public --add-port=4369/tcp --permanent
sudo firewall-cmd --zone=public --add-port=5671-5672/tcp --permanent
sudo firewall-cmd --zone=public --add-port=8883/tcp --permanent
sudo firewall-cmd --zone=public --add-port=15672/tcp --permanent
sudo firewall-cmd --zone=public --add-port=25672/tcp --permanent
sudo firewall-cmd --zone=public --add-port=61613-61614/tcp --permanent

sudo firewall-cmd --reload

sudo iptables -A INPUT -p tcp --dport 4369 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 25672 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 5672 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 15672 -j ACCEPT
```

***6- configurações inicias***

```
Adicione um usuário ao RebbitMQ, pois o usuário padrão só pode efetuar login no localhost.
sudo rabbitmqctl add_user admin M@rcus20316669

Atribuir permissões de administrador ao usuário criado.
sudo rabbitmqctl set_user_tags admin administrator

Uma vez criado, você pode listar usuários com o comando:
sudo rabbitmqctl list_users

Você também precisa adicionar um host virtual para poder usar o RabbitMQ
sudo rabbitmqctl add_vhost /jet-rabbit

Listar o host virtual:
sudo rabbitmqctl list_vhosts

Agora conceda ao usuário permissões para o Virtualhost:
sudo rabbitmqctl set_permissions -p /jet-rabbit admin ".*" ".*" ".*"

Listar as permissões do usuário.
sudo rabbitmqctl list_user_permissions admin
```

***7- configurando o cluster***
Depois que o RabbitMQ for instalado e configurado em todos os 2 nós, você poderá configurar o clustering.

```
você precisa colocar o mesmo cookie em todos os nós. Então podemos copiar o cookie do nó1 para os outros nós:
sudo cat /var/lib/rabbitmq/.erlang.cookie; echo

Agora cole o cookie no nó 2
sudo nano /var/lib/rabbitmq/.erlang.cookie

Salve o arquivo e reinicie o serviço no nó 2
sudo systemctl restart rabbitmq-server

Pare o aplicativo RabbitMQ e reinicie o servidor.
sudo rabbitmqctl stop_app

sudo rabbitmqctl reset
Resetting node rabbit@rabbitmq_02 ...

Agora junte as instâncias do RabbitMQ no nó 2 ao node1(rabbit-1)
sudo rabbitmq-server -detached
sudo rabbitmqctl join_cluster rabbit@rabbit-1

Agora inicie o aplicativo:
sudo rabbitmqctl start_app
Check the cluster status:
sudo rabbitmqctl cluster_status
```

***8- Configure RabbitMQ HA Policy***

```
A HA Policy habilita o espelhamento de fila em todos os nós do cluster. Isso pode ser definido usando o comando:
sudo rabbitmqctl set_policy ha-all "." '{"ha-mode":"all"}'
Setting policy "ha-all" for pattern "." to "{"ha-mode":"all"}" with priority "0" for vhost "/" ...

rabbitmqctl set_policy ha-fed \
    ".*" '{"federation-upstream-set":"all", "ha-sync-mode":"automatic", "ha-mode":"nodes", "ha-params":["rabbit@rabbit-1","rabbit@rabbit-2"]}' \
    --priority 1 \
    --apply-to queues

Verifique se a política foi criada***
sudo rabbitmqctl list_policies
```

***9- Enable the RabbitMQ web UI***

```
A interface web do RabbitMQ oferece uma maneira fácil de gerenciar/monitorar as instâncias do RabbitMQ. Habilite a interface web do RabbitMQ em todos os 3 nós com o comando:
sudo rabbitmq-plugins enable rabbitmq_management
sudo systemctl restart rabbitmq-server
Now access the web UI on one of the nodes, using the URL http://IP_address:15672

On this page, log in with the user-created earlier. On successful login, you will see the dashboard with all the nodes displayed.
```



