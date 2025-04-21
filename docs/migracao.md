# Migração para outro Servidor

Migrar o sistema para outro servidor, implica fazer uma instalação nova do código que implementa o sistema descrito em [Arquitectura do Sistema](./arquitectura.md).

O sistema pode ser descarregado directamente do repositório de git:

``` bash
git clone https://github.com/byteroad/ogcapi-simple.git
```

Entrar dentro do repositório:

``` bash
cd ogcapi-simple
```

Antes de serem iniciados os containers (tal como está descrito em [Gerir as Aplicações](tarefas.md#gerir-as-aplicacoes)), deve ser criado um ficheiro `.env`, na raiz desse repositório com as variáveis de ambiente. A forma mais simples é copiar o ficheiro que está na máquina ogcapi. Também devem ser copiados os certificados SSL, para a pasta `/etc/ssl` (ver [Substituição dos certificados SSL](./tarefas.md#substituicao-dos-certificados-ssl))

Depois, o sistema pode ser iniciado (no background) com:

``` bash
docker compose up -d
```

No caso de ser uma instalacão nova, o docker daemon vai puxar a última versão das imagens de docker utilizadas e fazer o build das imagens necessárias.

!!! info

    O sistema OGCAPI não tem a noção de estado, já que é recriado de cada vez que é iniciado. As mudanças são persistidas no código, permitindo que seja reproduzível em outras máquinas sem nenhum esforço adicional. 
    
    No entanto a aplicação de monitorização de logs (`geohealthcheck`) e a aplicação de monitorização de serviços (`matomo`) guardam dados (e configurações) que são criados depois do sistema estar em marcha e que portanto não estão descritos no código. Para migrar o matomo, é necessário copiar directoria `./matomo/data`. O [manual de utilizadores do GeoHealthCheck](https://docs.geohealthcheck.org/en/latest/admin.html) explica como exportar os dados para um ficheiro json e importar-los numa BD nova.

!!! warning

    Por defeito, o GeoHealthCheck é instalado com uma password não segura. Para actualizar a password depois do sistema estar a funcionar, podem ser seguidos [estes passos](https://docs.geohealthcheck.org/en/latest/admin.html#passwords), desde o interior do container.

    1. Entrar no container `ghc_runner`: `docker exec -it ghc_runner bash`.
    2. Entrar pasta `GeoHealthCheck`: `cd GeoHealthCheck/`
    3. Criar hash da password: `paver create_hash -p mypass` (substituir mypass pela password a utilizar)
    4. Install sqlite: `apt-get update && apt-get install sqlite3`
    5. Entrar dentro da BD: `sqlite3 DB/data.db`
    6. Actualizar a password usando o hash criado no passo 3.: `UPDATE user SET password = '<above hash-value>' WHERE username == 'myusername';`
    7. Sair da BD: '.q' 
