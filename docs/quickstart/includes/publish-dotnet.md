1. Altere para a pasta que contém o arquivo `.nupkg`.

1. Execute o seguinte comando, especificando o nome do pacote e substituindo o valor da chave pela chave da API:

    ```cli
    dotnet nuget push AppLogger.1.0.0.nupkg -k qz2jga8pl3dvn2akksyquwcs9ygggg4exypy3bhxy6w6x6 -s https://api.nuget.org/v3/index.json
    ```

1. O dotnet exibe os resultados do processo de publicação:

    ```output
    info : Pushing AppLogger.1.0.0.nupkg to 'https://www.nuget.org/api/v2/package'...
    info :   PUT https://www.nuget.org/api/v2/package/
    info :   Created https://www.nuget.org/api/v2/package/ 12620ms
    info : Your package was pushed.
    ```

Veja [dotnet nuget push](/dotnet/core/tools/dotnet-nuget-push).