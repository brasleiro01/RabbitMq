# RabbitMq

#Configuração de um cluster com 3 nós do RabbitMq

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
## install these dependencies from standard OS repositories
dnf install -y logrotate

Por fim, instale o Erlang e o RabbitMQ modernos:
## install RabbitMQ and zero dependency Erlang
dnf install -y erlang rabbitmq-server
```

***4- Inicialização***

```
systemctl enable rabbitmq-server

5- Como administrador, inicie e pare o servidor normalmente, por exemplo, usando systemctl:

systemctl start rabbitmq-server

systemctl status  rabbitmq-server

systemctl stop rabbitmq-server
```


6- Allow the port through the firewall:
sudo firewall-cmd --add-port=5672/tcp --permanent
sudo firewall-cmd --reload

7- Add a user to RebbitMQ since the default user can only log in to localhost.
sudo rabbitmqctl add_user admin M@rcus20316669

8- Assign administrator permissions to the created user
sudo rabbitmqctl set_user_tags admin administrator

9- You can set new password for admin RabbitMQ user with the following command:
sudo rabbitmqctl change_password admin Password@12

10- Once created, you can list users with the command:
sudo rabbitmqctl list_users

11- Você também precisa adicionar um host virtual para poder usar o RabbitMQ
sudo rabbitmqctl add_vhost /rabbit01

12- List the virtual host:
sudo rabbitmqctl list_vhosts

13- Agora conceda ao usuário permissões para o Virtualhost:
sudo rabbitmqctl set_permissions -p /rabbit01 admin ".*" ".*" ".*"

14- Listar as permissões do usuário.
sudo rabbitmqctl list_user_permissions admin

#3. Configure RabbitMQ Clustering
1- Depois que o RabbitMQ for instalado e configurado em todos os 3 nós, você poderá configurar o clustering.
Primeiro, habilite as portas necessárias através do firewall.
sudo firewall-cmd --add-port={4369/tcp,25672/tcp} --permanent
sudo firewall-cmd --reload
Em seguida, você precisa colocar o mesmo cookie em todos os nós. Então podemos copiar o cookie do nó1 para os outros nós:
$ sudo cat /var/lib/rabbitmq/.erlang.cookie; echo
XHSJNPBCYLXMZHRDQKMR

2- Agora cole o cookie nos nós 2 e 3
sudo nano /var/lib/rabbitmq/.erlang.cookie

3- Salve o arquivo e reinicie o serviço nos nós 2 e 3
sudo systemctl restart rabbitmq-server
Pare o aplicativo RabbitMQ e reinicie o servidor.
•	On node2 and node3
sudo rabbitmqctl stop_app
Stopping rabbit application on node rabbit@rabbitmq_02 ...

4- sudo rabbitmqctl reset
Resetting node rabbit@rabbitmq_02 ...
Agora junte as instâncias do RabbitMQ nos nós 2 e 3 ao node1(rabbitmq_01)
On node2 and node3
sudo rabbitmq-server -detached
sudo rabbitmqctl join_cluster rabbit@rabbitmq_01

No comando acima, lembre-se de usar apenas o nome do host, não com FQDN especificado. Por exemplo rabbitmq_01

5- Agora inicie o aplicativo:
sudo rabbitmqctl start_app
Check the cluster status:
sudo rabbitmqctl cluster_status

A partir da saída acima, todos podemos concordar que temos o cluster RabbitMQ em execução com 3 nós.

#4. Configure RabbitMQ HA Policy
1- A HA Policy habilita o espelhamento de fila em todos os nós do cluster. Isso pode ser definido usando o comando:
sudo rabbitmqctl set_policy ha-all "." '{"ha-mode":"all"}'
Setting policy "ha-all" for pattern "." to "{"ha-mode":"all"}" with priority "0" for vhost "/" ...

2- Verifique se a política foi criada.
sudo rabbitmqctl list_policies

#5. Enable the RabbitMQ web UI
1- A interface web do RabbitMQ oferece uma maneira fácil de gerenciar/monitorar as instâncias do RabbitMQ. Habilite a interface web do RabbitMQ em todos os 3 nós com o comando:
sudo rabbitmq-plugins enable rabbitmq_management
sudo systemctl restart rabbitmq-server
Allow the required ports through the firewall:
sudo firewall-cmd --zone=public --permanent --add-port=5671-5672/tcp --add-port=15672/tcp  --add-port=61613-61614/tcp --add-port=1883/tcp --add-port=8883/tcp
sudo firewall-cmd --reload
Now access the web UI on one of the nodes, using the URL http://IP_address:15672
On this page, log in with the user-created earlier. On successful login, you will see the dashboard with all the nodes displayed.
