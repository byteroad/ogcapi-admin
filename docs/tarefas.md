# Tarefas de Manutenção

Esta secção descreve algumas tarefas comuns de manutenção do servidor e da aplicação.

!!! danger

    A leitura desta secção não substitui a aprendizagem de como gerir uma máquina de linux, e como usar docker, docker compose ou git. São providenciados alguns recursos sobre este tema na secção [Referências e Recursos de Aprendizagem](./recursos.md).

## Manutenção do Servidor

O servidor deverá ser actualizado de forma regular, seguindo as boas práticas de gestão de uma máquina de Linux:

``` bash
sudo apt-get update
sudo apt-get upgrade
```

Algumas actualizações updates podem requerer o restart da máquina. 

!!! warning

    As LTS proporcionam 5 anos de actualizações de segurança; depois desse período deve-se fazer o upgrade da distribuição.

## Gerir as Aplicações

O sistema descrito em [Arquitectura do Sistema](arquitectura.md#arquitectura-do-sistema) pode ser gerido utilizando comandos standard de `docker compose`, desde a raiz do projeto (a pasta onde se encontra o ficheiro docker-compose.yml).

Iniciar as aplicações (em background):

``` bash
docker compose up -d
```

Visualizar os logs em tempo real:

``` bash
docker compose logs --follow
``` 

Parar as aplicações:

``` bash
docker compose down
```

Reiniciar as aplicações:

``` bash
docker compose restart
```

## Actualizar as Aplicações

O código do sistema descrito em [Arquitectura do Sistema](arquitectura.md#arquitectura-do-sistema) encontra-se num [repositório de git](https://github.com/byteroad/ogcapi-simple). Para instalar actualizações, é necessário descarregar as mudanças desde o repositório original, fazer o build dos containers caso necessário e voltar a lançar os containers.

1. Descarregar as mudanças remotas para o repositório local: `git pull`.
2. Parar as aplicações (ver [Gerir as Aplicações](./tarefas.md#gerir-as-aplicacoes)).
3. Fazer o build das imagens de docker: `docker compose build`.
4. Iniciar as aplicações (em background) (ver [Gerir as Aplicações](./tarefas.md#gerir-as-aplicacoes)).

!!! tip

    O sistema usa algumas imagens de base que podem beneficiar de actualizações. Essas imagens podem ser actualizadas usando o comando `docker compose pull` e depois reiniciando as aplicações (ver [Gerir as Aplicações](./tarefas.md#gerir-as-aplicacoes)).

## Assegurar-se de que o GHC consegue aceder ao Apache

De cada vez que os containers são criados de novo (por exemplo, com um restart do servidor), é importante assegurar-se de que o container GHC consegue aceder a `https://ogcapi.dgterritorio.gov.pt/`. Se não for assim, o GHC vai falhar a monitorização dos serviços, assumindo que eles não estão a funcionar. Os passos para resolver esta situação estão descritos [aqui](./arquitectura.md#servicos-que-monitorizam-outros-servicos).

## Substituição dos certificados SSL

Os certificados SSL estão guardados na pasta `/etc/cert`. No caso de instalar novos certificados com o mesmo nome, será necessário parar as aplicações e voltar a iniciar-las, para que os volumes dos containers sejam criados de novo, já com os novos certificados. No caso de os certificados terem nomes diferentes ou estarem noutra localização, é necessário também actualizar o mapping no [docker-compose.yml](https://github.com/byteroad/ogcapi-simple/blob/master/docker-compose.yml#L38) para reflectir essas mudanças:

``` yaml     
      - /etc/cert/2ogcapi.pem:/usr/local/apache2/conf/ogcapi/fullchain.pem
      - /etc/cert/ogcapi_privkey4.pem:/usr/local/apache2/conf/ogcapi/privkey.pem
      - /etc/cert/2logs.pem:/usr/local/apache2/conf/matomo/fullchain.pem  
      - /etc/cert/logs_privkey4.pem:/usr/local/apache2/conf/matomo/privkey.pem
      - /etc/cert/1ghc.pem:/usr/local/apache2/conf/health/fullchain.pem  
      - /etc/cert/ghc_privkey1.pem:/usr/local/apache2/conf/health/privkey.pem
```
!!! tip

    Em cada uma destas strings de [YAML](https://en.wikipedia.org/wiki/YAML), a primeira parte (antes do `:`) representa o caminho e nome da chave publica ou privada do certificado (por exemplo, `/etc/cert/2ogcapi.pem`).

## Contribuir Mudanças nas Aplicações

Contribuiçoes para o código do sistema, incluindo alterações ao ficheiro `docker-compose.yml` devem ser feitas directamente no [repositório git](https://github.com/byteroad/ogcapi-simple), através dos mecanismos normais de contribuição do git.