# Instalação Zabbix 
A instalação foi feita no Ubuntu Server LTS 18.04.2 - Postgresql

## Sobre o Projeto 
O presente projeto tem como objetivo, auxiliar na instalação e configurações iniciais da ferramenta de gerenciamento de redes Zabbix. 

## Repositório com todas as versões 
Link: https://repo.zabbix.com/zabbix/

## Versão instalada para os testes 
``` bash
#Download do pacote 
wget https://repo.zabbix.com/zabbix/4.2/ubuntu/pool/main/z/zabbix-release/zabbix-release_4.2-1%2Bbionic_all.deb

#Instalar o pacote
dpkg - "Nome do pacote baixado"

```

## Pacotes necessários para instalação Postgresql 
``` bash

sudo apt-get install curl ca-certificates

curl https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

``` 

## Criar arquivo do source.list

``` bash

#Descobrir codinome da versão para substituir no arquivo “pgdg.list”
lsb_release -c

#Para download do postgresql via gerenciador de pacote e adicionar linha
vim /etc/apt/sources.list.d/pgdg.list

#Adicionar linha e sair do arquivo e salvar(Substituir o “bionic” pelo nome da sua versão)
deb http://apt.postgresql.org/pub/repos/apt/ bionic-pgdg main

#Atualizar repositório
apt update

``` 

## Instalação postgresql
``` bash

sudo apt-get install postgresql-11

``` 

## Criação do banco de dados Zabbix no Postgresql

``` bash
#Logando com o usuário Postgres
su - postgres

#Acessando o console 
psql

#Creando base de dado Zabbix
CREATE DATABASE zabbixdb;

#Criando login Zabbix
CREATE ROLE zabbix LOGIN;

#Definir senha 
\password zabbix

#Sair do console Postgres
\q

#Sair 
exit

``` 
## Configurando permissões

``` bash
# Permite que o usuário zabbix possa se conectar ao Zabbix BD
vim /etc/postgresql/11/main/pg_hba.conf

# Descobrir o IP
ip as

# Adicionar sua linha aqui (Substituir o IP pelo seu)
host zabbixdb zabbix 192.168.99.10/32 md5

# Reiniciar Postgresql
service postgresql restart

#Testar comunicação com o banco de dados usuário zabbix
su - postgres

#Acessar BD Zabbix
psql -h localhost -U zabbix zabbixdb

#Sair da console postgresql
\q  

``` 

## Instalação Zabbix Server

``` bash

apt-get install zabbix-server-pgsql

# Acessado usuário Postgres 
su - postgres

# Acessar diretório
cd /usr/share/doc/zabbix-server-pgsql/

# Criando tabelas
zcat create.sql.gz | psql zabbixdb

# Logando o BD 
psql zabbixdb

# Permissões para usuário zabbix poder acessar suas tabelas
GRANT SELECT,UPDATE,DELETE,INSERT ON ALL TABLES IN SCHEMA public TO zabbix;

# Sair
\q
exit 

# Acessar arquivo
vim /etc/zabbix/zabbix_server.conf

# Adicionar os parâmetros
DBHost=localhost
DBName=zabbixdb
DBUser=zabbix
DBPassword=zabbix

# Reiniciar zabbix-server
service zabbix-server restart

``` 

## Instalar PHP 7.0

``` bash

apt-get install php7.2 php7.2-pgsql php7.2-bcmath php7.2-gd php7.2-mbstring php7.2-xml php7.2-gettext php7.2-ldap

# Backup do arquivo php.ini
cp /etc/php/7.2/apache2/php.ini /etc/php/7.2/apache2/php.ini.backup

# Alterando parâmetros do arquivo (Recomendado pelo Zabbix)
sed -i 's/max_execution_time/\;max_execution_time/g' /etc/php/7.2/apache2/php.ini
echo 'max_execution_time=300'>> /etc/php/7.2/apache2/php.ini				
sed -i 's/max_input_time/\;max_input_time/g' /etc/php/7.2/apache2/php.ini
echo 'max_input_time=300' >> /etc/php/7.2/apache2/php.ini
sed -i 's/date.timezone/\;date.timezone/g' /etc/php/7.2/apache2/php.ini
echo 'date.timezone=America/Sao_Paulo' >> /etc/php/7.2/apache2/php.ini
sed -i 's/post_max_size/\;post_max_size/g' /etc/php/7.2/apache2/php.ini
echo 'post_max_size=16M' >> /etc/php/7.2/apache2/php.ini

# Instalação Frontend
apt-get install zabbix-frontend-php

# Reiniciar apache
service apache2 restart

# Ativar Zabbix Server no boot
systemctl enable zabbix-server

# Iniciando Zabbix Server 
service zabbix-server restart

# Abrir navegador e acessar Wizard Frontend
http://IPDOSERVIDOR/zabbix


``` 
