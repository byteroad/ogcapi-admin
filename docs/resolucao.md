# Resolução de Problemas

Esta secção compila alguns problemas comuns que podem surgir, e as soluções identificadas. Caso identifiquem outros problemas/soluções que mereçam a pena ser adicionados ou actualizados, podem contribuir utilizando os mecanismos identificados [nesta secção](./contribuir.md).

!!! question

    Porque é que a colecção dos ortos, cadastro ou caop deixou de funcionar?
    
Estas colecções dependem de servidores externos. Devem ser verificados se estes serviços estão acessíveis:

* ortos -> `https://ortos.dgterritorio.gov.pt/wms/ortosat2023`
* cadastro -> BD inspire
* caop -> BD caop

Para mais detalhes, consultar o [ficheiro de configuração da pygeoapi](https://github.com/byteroad/ogcapi-simple/blob/master/pygeoapi/docker.config.yml).

!!! question

    Porque é que as novas tabelas que foram adicionadas ao PostGIS não estão a ser disponibilizadas como tiles?

O `martin` lê as tabelas da BD quando inicia. Por esse motivo, se forem adicionadas novas tabelas na BD, o martin não irá publicar-las até que o servidor de tiles relevante seja reiniciado.

```bash
docker compose restart ogcapi-simple-tiles-1
docker compose restart tiles_caop
docker compose restart tiles_inspire
```

 Mais informações sobre o serviço [`martin`](./arquitectura.md#servicos-de-tiles).

!!! question

    A máquina de Ubuntu foi reiniciada, mas os serviços estão em baixo. Como iniciar-los?

Se por algum motivo os serviços estiverem em baixo, as aplicacões podem ser iniciadas seguindo as instrucões [nesta secção](tarefas.md#gerir-as-aplicacoes) 

!!! question

    A monitorização de serviços indica que eles não estão a funcionar, quando na realidade estão. Como resolver esta situação?

o `geohealthcheck` perdeu as configurações do `/etc/hosts` (por exemplo com um restart da máquina) e não consegue aceder ao IP da OGC API. Os passos para resolver esta situação estão explicados [aqui](./arquitectura.md#servicos-que-monitorizam-outros-servicos).
