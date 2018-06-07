---
title: Comando de exclusão de CLI do NuGet
description: Referência para o comando de exclusão de nuget.exe
author: karann-msft
ms.author: karann
manager: unnir
ms.date: 01/18/2018
ms.topic: reference
ms.openlocfilehash: c0f33dd5475521da47972a6f032ac6ea86d98c83
ms.sourcegitcommit: 2a6d200012cdb4cbf5ab1264f12fecf9ae12d769
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34817172"
---
# <a name="delete-command-nuget-cli"></a>Excluir um comando NuGet CLI)

**Aplica-se a:** pacote publicação &bullet; **versões com suporte:** todos

Exclui ou unlists um pacote de uma origem do pacote. Para nuget.org, o comando Excluir [unlists o pacote](../policies/deleting-packages.md).

## <a name="usage"></a>Uso

```cli
nuget delete <packageID> <packageVersion> [options]
```

onde `<packageID>` e `<packageVersion>` identificar o pacote exato para excluir ou remover da lista. O comportamento exato depende da fonte. Para pastas locais, por exemplo, o pacote foi excluído; para o pacote do nuget.org está na lista.

## <a name="options"></a>Opções

| Opção | Descrição |
| --- | --- |
| apiKey | A chave de API para o repositório de destino. Se não estiver presente, o especificado no arquivo de configuração é usado. |
| ConfigFile | O arquivo de configuração do NuGet para aplicar. Se não for especificado, `%AppData%\NuGet\NuGet.Config` (Windows) ou `~/.nuget/NuGet/NuGet.Config` (Mac/Linux) é usado.|
| ForceEnglishOutput | *(3.5 +)*  Força nuget.exe para ser executado usando uma cultura invariável, com base em inglês. |
| Ajuda | Exibe informações de ajuda para o comando. |
| NonInteractive | Suprime avisos para a entrada do usuário ou confirmações. |
| Origem | Especifica a URL do servidor. A URL para nuget.org é `https://api.nuget.org/v3/index.json`. Para feeds privados, substitua o nome de host, por exemplo, *%hostname%/api/v3*. |
| Detalhamento | Especifica a quantidade de detalhes exibidos na saída: *normal*, *silencioso*, *detalhadas*. |

Consulte também [variáveis de ambiente](cli-ref-environment-variables.md)

## <a name="examples"></a>Exemplos

```cli
nuget delete MyPackage 1.0

nuget delete MyPackage 1.0 -Source http://package.contoso.com/source -apikey A1B2C3
```