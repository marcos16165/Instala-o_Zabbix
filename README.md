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

``` 
