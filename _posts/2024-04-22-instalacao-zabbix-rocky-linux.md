---
layout: post
title: Instalando o Zabbix no Rocky Linux
subtitle: mantendo os olhos no seu servidor!
thumbnail-img: /assets/img/zabbix/logo-zabbix.webp
gh-repo: robsonafsilva/notepad
gh-badge: [star, fork, follow]
tags: [zabbix, monitoramento, linux, mysql, Rocky Linux]
comments: true
mathjax: true
author: Robson
---



**O que é o Zabbix**

O Zabbix é um software que monitora numerosos parâmetros de rede, a saúde e integridade de servidores, máquinas virtuais, aplicações, serviços, banco de dados, websites, a nuvem e muito mais. O Zabbix usa um mecanismo flexível de notificação que permite aos usuários configurar alertas baseados em e-mail para praticamente qualquer evento. Isso permite uma resposta rápida para problemas do servidor. O Zabbix oferece um excelente recurso de relatórios e visualização de dados baseados em dados armazenados. Isso torna o Zabbix ideal para gerenciamento de capacidade.


Aqui vamos instalar o Zabbix no Rocky Linux 9, usando o arroz com feijão do mundo Linux. MySQL e Apache

Começaremos instalando o MySQL, lembrando que estamos logados como root.

```dnf install mysql-server```

Subindo o serviço do MySQL.

```systemctl start mysqld.service```

Checando se o serviço subiu.

```systemctl status mysqld```

Colocando o MySQL para inicializa com o sistema.

```systemctl enable mysqld```

Agora precisamos rodar o script de pós instalação. 

```mysql_secure_installation```

A saída do comando com alguns comentários e as resposta que costumo usar.
~~~

[root@server ~]# mysql_secure_installation

Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

** se desejar configurar o MySQL para só acetar senhas complexas digite y **
** recomendado em produção ** 
Press y|Y for Yes, any other key for No: 

Please set the password for root here.

New password:  **Aqui você coloca a senha que deseja utilizar no db**
Re-enter new password: **senha novamente**

By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

**remover usuário anonimo criado na instalação**
Remove anonymous users? (Press y|Y for Yes, any other key for No) : y 
Success.

Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

**desabilitar o login com usuário root**
Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


**remover o banco de dados de teste.**
**pode ser útil caso esteja interessado em aprender sobre banco de dados.**
Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.
Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

**Recarregar a tabela de permissões** 
Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done!
~~~

Acessando o mysql
~~~
mysqladmin -u root -p
**aqui digite sua senha**
~~~


Bora criar o banco de dados - fique atendo a senha no meio dos comando abaixo:

Aqui criamos o banco que o zabbix vai utilizar, o usuário para acesso ao banco,
acertamos as permissões e setamos a variável log_bin_trust_function_creators para true.
Esta variável controla se o log binário deve confiar nos criadores de funções armazenadas 
para não criar funções armazenadas que causarão eventos inseguros.

~~~
mysql> create database zabbix character set utf8mb4 collate utf8mb4_bin;
mysql> create user zabbix@localhost identified by 'password'; 
mysql> grant all privileges on zabbix.* to zabbix@localhost;
mysql> set global log_bin_trust_function_creators = 1;
mysql> quit;
~~~

Agora adicionamos o repositórios do zabbix. E damos uma limapda no cache do dnf.
Confira no site do zabbix para saber qual a versão mais atual. Ou escolha uma LTS
~~~
rpm -Uvh https://repo.zabbix.com/zabbix/6.4/rhel/9/x86_64/zabbix-release-6.4-1.el9.noarch.rpm
dnf clean all
~~~

Agora vamos instalar o servidor, o frontend e o agente do Zabbix.
~~~
dnf install zabbix-server-mysql zabbix-web-mysql zabbix-apache-conf zabbix-sql-scripts zabbix-selinux-policy zabbix-agent
~~~

Vamos importar o schema do banco de dados.
Não esquece de inform a senha do usuário zabbix depois de parâmatro -p

~~~
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p senha
~~~

Vamos desabilitar log_bin_trust_function_creators
~~~
mysql -uroot -p
password
mysql> set global log_bin_trust_function_creators = 0;
mysql> quit;
~~~

Configure o banco de dados para o servidor Zabbix no arquivo de configuração.

Editar arquivo /etc/zabbix/zabbix_server.conf

Procure a linha

DBPAssword=password

descomente e troque password pela senha do usuário zabbix criado nom MySQL.

Inicie o servidor Zabbix e os processos do agente e adicione para inicializar no boot.
~~~
systemctl restart zabbix-server zabbix-agent httpd php-fpm
systemctl enable zabbix-server zabbix-agent httpd php-fpm
~~~

Precisamos abrir a porta no firewall
~~~
firewall-cmd --permanent --zone=public --add-port=80/tcp
~~~

Agora você pode processiguir com  a instalação via web acessando:

http://ip-do-host/zabbix

**Aqui você pode selecionar o idioma**
![Configuração Idioma](/assets/img/zabbix/install_1.png)

**Confira se o cheklist de intalação está ok**
![Check Instalação](/assets/img/zabbix/install_2.png)

**Aqui você configura os dados de acesso ao banco**
![Configuração MySQL](/assets/img/zabbix/zabbix_install_3a.png)

**Configurações do servidor**

o zabbix server name é o mesmo se seu hostname.
Configure o time zone e escolha uma cor para o tema.
![Configuração servidor](/assets/img/zabbix/install_4.png)
![Configuração servidor](/assets/img/zabbix/install_5.png)
![Configuração servidor](/assets/img/zabbix/install_7.png)

**Agora é só logar.**

Usuario Admin e a senha zabbix.

![Configuração servidor](/assets/img/zabbix/login.png)

[Documentação Oficial](https://www.zabbix.com/documentation/6.4/en/manual/introduction)

