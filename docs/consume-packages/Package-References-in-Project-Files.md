---
title: Formato PackageReference do NuGet (referências de pacote em arquivos de projeto)
description: Veja detalhes sobre o PackageReference de NuGet em arquivos de projeto compatível com o NuGet 4.0 e posterior e o VS2017 e .Net Core 2.0
author: karann-msft
ms.author: karann
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: e4df15be1f29e2c611876aaa49e16ac7d1823938
ms.sourcegitcommit: be9c51b4b095aea40ef41bbea7e12ef0a194ee74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53248449"
---
# <a name="package-references-packagereference-in-project-files"></a>Referências de pacote (PackageReference) em arquivos de projeto

As referências de pacote, usando o nó `PackageReference`, gerenciam as dependências do NuGet diretamente nos arquivos de projeto (em vez de precisar de um arquivo `packages.config` separado). Usar o PackageReference, como ele é chamado, não afeta os outros aspectos do NuGet; por exemplo, as configurações nos arquivos `NuGet.config` (inclusive nas origens de pacote) ainda são aplicadas, conforme explicado em [Configurando o comportamento do NuGet](configuring-nuget-behavior.md).

Com o PackageReference, você também pode usar condições do MSBuild para escolher as referências de pacote por estrutura de destino, por configuração, por plataforma ou por outros agrupamentos. Ele também proporciona um controle refinado sobre as dependências e o fluxo de conteúdo. (Para obter mais detalhes, veja [Empacotamento e restauração do NuGet como destinos do MSBuild](../reference/msbuild-targets.md).)

Por padrão, o PackageReference é usado para projetos do .NET Core, para projetos do .NET Standard e para projetos da UWP direcionados ao Windows 10 Build 15063 (Atualização para Criadores) e posterior, com exceção dos projetos da C ++ UWP. Os projetos do .NET Framework são compatíveis com o PackageReference, mas, no momento, contam com `packages.config` como padrão. Para usar PackageReference, migre as dependências de `packages.config` para o arquivo de projeto e remova packages.config.

## <a name="adding-a-packagereference"></a>Adicionar um PackageReference

Adicione uma dependência ao arquivo de projeto usando a seguinte sintaxe:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

## <a name="controlling-dependency-version"></a>Controlar a versão de dependência

A convenção para especificar a versão de um pacote é a mesma que usar `packages.config`:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

No exemplo acima, 3.6.0 significa qualquer versão que é >=3.6.0 com preferência para a versão mais antiga, conforme descrito em [Controle de versão do pacote](../reference/package-versioning.md#version-ranges-and-wildcards).

## <a name="using-packagereference-for-a-project-with-no-packagereferences"></a>Usando PackageReference para um projeto sem PackageReferences
Avançado: Se você não tem pacotes instalados em um projeto (não tem PackageReferences no arquivo de projeto nem um arquivo packages.config), mas deseja que o projeto seja restaurado como o estilo PackageReference, você pode definir uma propriedade de projeto RestoreProjectStyle como PackageReference em seu arquivo de projeto.
```xml
<PropertyGroup>
    <!--- ... -->
    <RestoreProjectStyle>PackageReference</RestoreProjectStyle>
    <!--- ... -->
</PropertyGroup>    
```
Isso poderá ser útil se você fizer referência a projetos que são do estilo PackageReference (projetos de estilo csproj ou SDK existentes). Isso permitirá que os pacotes aos quais esses projetos fazem referência sejam referenciados "transitivamente" pelo seu projeto.

## <a name="floating-versions"></a>Versões flutuantes

[Versões flutuante](../consume-packages/dependency-resolution.md#floating-versions) são compatíveis com `PackageReference`:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.*" />
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0-beta*" />
    <!-- ... -->
</ItemGroup>
```

## <a name="controlling-dependency-assets"></a>Controlar ativos de dependência

Você pode estar usando uma dependência puramente como uma estrutura de desenvolvimento e pode não querer expor isso aos projetos que consumirão o pacote. Nesse cenário, você pode usar os metadados do `PrivateAssets` para controlar esse comportamento.

```xml
<ItemGroup>
    <!-- ... -->

    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0">
        <PrivateAssets>all</PrivateAssets>
    </PackageReference>

    <!-- ... -->
</ItemGroup>
```

As seguintes marcas de metadados controlam ativos de dependência:

| Marca | Descrição | Valor padrão |
| --- | --- | --- |
| IncludeAssets | Esses ativos serão consumidos | all |
| ExcludeAssets | Esses ativos não serão consumidos | nenhum |
| PrivateAssets | Esses ativos serão consumidos, mas não fluem para o projeto pai | contentfiles;analyzers;build |

Os valores permitidos para essas marcas são os seguintes, com vários valores separados por ponto e vírgula, exceto com `all` e `none`, que devem aparecer sozinhos:

| Valor | Descrição |
| --- | ---
| compilar | Conteúdo da pasta `lib` e controles se seu projeto puder compilar em relação aos assemblies dentro da pasta |
| tempo de execução | Conteúdo da pasta `lib` e `runtimes` pasta e controles se esses assemblies forem copiados para o diretório de saída de compilação |
| contentFiles | O conteúdo da pasta `contentfiles` |
| build | Objetos e propriedades na pasta `build` |
| analisadores | Analisadores de .NET |
| nativa | O conteúdo da pasta `native` |
| nenhum | Nenhuma das opções acima é usada. |
| all | Todas as anteriores (exceto `none`) |

No exemplo a seguir, tudo, exceto os arquivos de conteúdo do pacote, poderia ser consumido pelo projeto e tudo, exceto analisadores e arquivos de conteúdo, fluiria para o projeto pai.

```xml
<ItemGroup>
    <!-- ... -->

    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0">
        <IncludeAssets>all</IncludeAssets>
        <ExcludeAssets>contentFiles</ExcludeAssets>
        <PrivateAssets>contentFiles;analyzers</PrivateAssets>
    </PackageReference>

    <!-- ... -->
</ItemGroup>
```

Observe que, como `build` não está incluído em `PrivateAssets`, destino e objetos *fluirão* para o projeto pai. Considere, por exemplo, que a referência acima é usada em um projeto que compila um pacote do NuGet chamado AppLogger. O AppLogger pode consumir os destinos e objetos de `Contoso.Utility.UsefulStuff`, bem como projetos que consomem AppLogger.

## <a name="adding-a-packagereference-condition"></a>Adicionar uma condição de PackageReference

É possível usar uma condição para controlar se um pacote está incluído, em que as condições podem usar qualquer variável MSBuild ou uma variável definida no arquivo de destinos ou objetos. No entanto, no momento, somente a variável `TargetFramework` é compatível.

Por exemplo, digamos que seu destino é `netstandard1.4` e `net452`, mas tem uma dependência que é aplicável somente para `net452`. Nesse caso, não é aconselhável ter um projeto `netstandard1.4` que está consumindo seu pacote para adicionar essa dependência desnecessária. Para evitar isso, você deve especificar uma condição no `PackageReference` da seguinte maneira:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Newtonsoft.Json" Version="9.0.1" Condition="'$(TargetFramework)' == 'net452'" />
    <!-- ... -->
</ItemGroup>
```

Um pacote compilado usando este projeto mostrará que Newtonsoft.Json está incluído como uma dependência somente para um destino `net452`:

![O resultado da aplicação de uma condição em PackageReference com o VS2017](media/PackageReference-Condition.png)

As condições também podem ser aplicadas no nível de `ItemGroup` e serão aplicadas a todos os elementos `PackageReference` filhos:

```xml
<ItemGroup Condition = "'$(TargetFramework)' == 'net452'">
    <!-- ... -->
    <PackageReference Include="Newtonsoft.Json" Version="9.0.1" />
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

## <a name="locking-dependencies"></a>Bloqueio de dependências
*Esse recurso está disponível no NuGet **4.9** ou superior e no Visual Studio 2017 **15.9** ou superior.*

A entrada da restauração do NuGet é um conjunto de Referências de pacote do arquivo de projeto (dependências de nível superior ou diretas), e a saída é um fechamento completo de todas as dependências do pacote, incluindo as dependências transitivas. Se a lista PackageReference de entrada não tiver sido alterada, o NuGet tenta sempre produzir o mesmo fechamento completo das dependências de pacotes. No entanto, em alguns cenários, isso não poderá ser feito. Por exemplo:

* Quando você usa versões flutuantes como `<PackageReference Include="My.Sample.Lib" Version="4.*"/>`. Embora a intenção aqui seja derivar para a versão mais recente sempre que uma restauração de pacotes ocorrer, há cenários em que os usuários exigem que o grafo seja bloqueado em uma determinada versão mais recente e derive para uma versão posterior, se disponível, mediante um gesto explícito.
* Uma versão mais recente do pacote que corresponde aos requisitos de versão do PackageReference é publicada. Por exemplo, 

  * Dia 1: se você tiver especificado `<PackageReference Include="My.Sample.Lib" Version="4.0.0"/>`, mas as versões disponíveis nos repositórios do NuGet forem 4.1.0, 4.2.0 e 4.3.0. Nesse caso, o NuGet teria que ser resolvido como 4.1.0 (mais próximo da versão mínima)

  * Dia 2: A versão 4.0.0 é publicada. O NuGet agora encontrará a correspondência exata e iniciará a resolução como 4.0.0

* Uma determinada versão de pacote é removida do repositório. Embora nuget.org não permita exclusões de pacotes, nem todos os repositórios de pacotes possuem essas restrições. Isso faz com que o NuGet encontre a melhor correspondência quando não puder resolver a versão excluída.

### <a name="enabling-lock-file"></a>Habilitar o arquivo de bloqueio
Para persistir o fechamento completo das dependências de pacotes, você pode optar pelo recurso de bloqueio de arquivos definindo a propriedade `RestorePackagesWithLockFile` do MSBuild para seu projeto:

```xml
<PropertyGroup>
    <!--- ... -->
    <RestorePackagesWithLockFile>true</RestorePackagesWithLockFile>
    <!--- ... -->
</PropertyGroup>    
```

Se essa propriedade for definida, a restauração do NuGet gerará um arquivo de bloqueio – arquivo `packages.lock.json` no diretório raiz do projeto que lista todas as dependências do pacote. 

> [!Note]
> Se um projeto tiver o arquivo `packages.lock.json` no diretório raiz, o arquivo de bloqueio sempre será usado com a restauração, mesmo que a propriedade `RestorePackagesWithLockFile` não esteja definida. Portanto, outra maneira de optar por esse recurso é através da criação um arquivo `packages.lock.json` fictício em branco no diretório raiz do projeto.

### <a name="restore-behavior-with-lock-file"></a>Comportamento `restore` com o arquivo de bloqueio
Se houver um arquivo de bloqueio para o projeto, o NuGet usará esse arquivo de bloqueio para executar `restore`. O NuGet faz uma verificação rápida para ver se houve alguma alteração nas dependências do pacote, conforme mencionado no arquivo do projeto (ou nos arquivos dependentes dos projetos) e, se não houver alterações, ele apenas restaurará os pacotes mencionados no arquivo de bloqueio. Não há reavaliações das dependências do pacote.

Se o NuGet detectar uma alteração nas dependências definidas, conforme mencionado nos arquivos de projeto, ele reavaliará o grafo do pacote e atualizará o arquivo de bloqueio para refletir o novo fechamento do pacote para o projeto.

Para CI/CD e outros cenários, em que você não deseja alterar as dependências de pacote rapidamente, é possível configurar o `lockedmode` como `true`:

Para dotnet.exe, execute:
```
> dotnet.exe restore --locked-mode
```

Para msbuild.exe, execute:
```
> msbuild.exe -t:restore -p:RestoreLockedMode=true
```

Você também pode definir essa propriedade condicional do MSBuild em seu arquivo de projeto:
```xml
<PropertyGroup>
    <!--- ... -->
    <RestoreLockedMode>true</RestoreLockedMode>
    <!--- ... -->
</PropertyGroup> 
```

Se o modo de bloqueio for `true`, a restauração ocorrerá nos pacotes exatos conforme listado no arquivo de bloqueio ou não ocorrerá se você tiver atualizado as dependências de pacote definidas para o projeto após a criação do arquivo de bloqueio.

### <a name="make-lock-file-part-of-your-source-repository"></a>Tornar o arquivo de bloqueio parte de seu repositório de origem
Se você estiver compilando um aplicativo, um executável e o projeto em questão estarão no início da cadeia da dependência, portanto, verifique o arquivo de bloqueio no repositório do código-fonte para que o NuGet possa utilizá-lo durante a restauração.

No entanto, se seu projeto for um projeto de biblioteca que não é enviado ou um projeto de código comum do qual outros projetos dependem, você **não deverá** fazer check-in do arquivo de bloqueio como parte de seu código-fonte. Não há mal nenhum em manter o arquivo de bloqueio. No entanto, as dependências do pacote bloqueado do projeto de código comum não podem ser usadas, conforme listado no arquivo de bloqueio, durante a restauração/compilação de um projeto que depende desse projeto de código comum.

Por exemplo:
```
ProjectA
  |------> PackageX 2.0.0
  |------> ProjectB
             |------>PackageX 1.0.0
```
Se `ProjectA` tiver uma dependência em uma versão `2.0.0` do `PackageX`, além de referências `ProjectB` que dependem da versão `1.0.0` do `PackageX`, o arquivo de bloqueio de `ProjectB` listará uma dependência na versão `1.0.0` do `PackageX`. No entanto, quando `ProjectA` for criado, seu arquivo de bloqueio conterá uma dependência na versão **`2.0.0`** do `PackageX` e **não** na versão `1.0.0`, conforme listado no arquivo de bloqueio de `ProjectB`. Portanto, o arquivo de bloqueio de um projeto de código comum tem pouco a dizer sobre os pacotes resolvidos de projetos que dependem dele.

### <a name="lock-file-extensibility"></a>Extensibilidade do arquivo de bloqueio
Você pode controlar vários comportamentos de restauração com o arquivo de bloqueio conforme descrito abaixo:

| Opção | Opção equivalente do MSBuild | 
|:---  |:--- |
| `--use-lock-file` | Uso do arquivo de bloqueio de um projeto pelas inicializações. Como alternativa, você pode definir a propriedade `RestorePackagesWithLockFile` no arquivo de projeto | 
| `--locked-mode` | Habilita o modo de bloqueio para a restauração. Isso é útil em cenários de CI/CD em que você gostaria de obter os builds repetidos. Isso também pode ser feito através da definição da propriedade `RestoreLockedMode` do MSBuild como `true` |  
| `--force-evaluate` | Esta opção é útil com pacotes que têm a versão flutuante definida no projeto. Por padrão, a restauração do NuGet não atualizará automaticamente a versão do pacote em cada restauração, a menos que você execute a restauração com a opção `--force-evaluate`. |
| `--lock-file-path` | Define o local de um arquivo de bloqueio personalizado para um projeto. Isso também pode ser feito através da definição da propriedade `NuGetLockFilePath` do MSBuild. Por padrão, o NuGet é compatível com `packages.lock.json` no diretório raiz. Se você tiver vários projetos no mesmo diretório, o NuGet oferecerá suporte ao arquivo de bloqueio `packages.<project_name>.lock.json` específico do projeto |
