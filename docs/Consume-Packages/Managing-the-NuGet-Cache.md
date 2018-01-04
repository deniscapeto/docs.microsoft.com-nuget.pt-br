---
title: Como gerenciar o cache de pacotes no NuGet | Microsoft Docs
author: kraigb
ms.author: kraigb
manager: ghogen
ms.date: 7/26/2017
ms.topic: article
ms.prod: nuget
ms.technology: 
ms.assetid: 3932217d-780d-4bd1-ad15-767acd3e8870
description: "Como gerenciar os diferentes caches de pacote do NuGet que existem em um computador, os quais são usados durante a instalação ou restauração de pacotes."
keywords: Cache de pacote do NuGet, cache de pacote, caches do NuGet, gerenciar caches, cache local do NuGet, cache global do NuGet, comando locals do NuGet, limpar um cache
ms.reviewer:
- karann-msft
- unniravindranathan
ms.openlocfilehash: 6e76c219ba420eb285af20e67b26dcdceebb6bab
ms.sourcegitcommit: d0ba99bfe019b779b75731bafdca8a37e35ef0d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2017
---
# <a name="managing-the-nuget-cache"></a><span data-ttu-id="fb07a-104">Gerenciando o cache do NuGet</span><span class="sxs-lookup"><span data-stu-id="fb07a-104">Managing the NuGet cache</span></span>

<span data-ttu-id="fb07a-105">O NuGet gerencia vários caches locais para evitar o download de pacotes que já estão no computador e para fornecer suporte offline.</span><span class="sxs-lookup"><span data-stu-id="fb07a-105">NuGet manages several local caches to avoid downloading packages that are already on the computer, and to provide offline support.</span></span> <span data-ttu-id="fb07a-106">O NuGet 2.8 ou superior reverterá automaticamente para o cache ao instalar ou reinstalar pacotes sem uma conexão de rede.</span><span class="sxs-lookup"><span data-stu-id="fb07a-106">NuGet 2.8+ automatically falls back to the cache when installing or reinstalling packages without a network connection.</span></span>

<span data-ttu-id="fb07a-107">Locais de cache estão disponíveis usando o [comando locals](../tools/cli-ref-locals.md):</span><span class="sxs-lookup"><span data-stu-id="fb07a-107">Cache locations are available using the [locals command](../tools/cli-ref-locals.md):</span></span>

```
nuget locals all -list
```

<span data-ttu-id="fb07a-108">A saída é a seguinte:</span><span class="sxs-lookup"><span data-stu-id="fb07a-108">Typical output is as follows:</span></span>

    http-cache: C:\Users\user\AppData\Local\NuGet\v3-cache   #NuGet 3.x+ cache
    packages-cache: C:\Users\user\AppData\Local\NuGet\Cache  #NuGet 2.x cache
    global-packages: C:\Users\user\.nuget\packages\          #Global packages folder
    temp: C:\Users\user\AppData\Local\Temp\NuGetScratch      #Temp folder

<span data-ttu-id="fb07a-109">Se você encontrar problemas de instalação do pacote ou se desejar garantir que você está instalando pacotes de uma galeria remota, use a opção `locals -clear`:</span><span class="sxs-lookup"><span data-stu-id="fb07a-109">If you encounter package installation problems or otherwise want to ensure that you're installing packages from a remote gallery, use the `locals -clear` option:</span></span>

```
nuget locals http-cache -clear        #Clear the 3.x+ cache
nuget locals packages-cache -clear    #Clear the 2.x cache
nuget locals global-packages -clear   #Clear the global packages folder
nuget locals temp -clear              #Clear the temporary cache
nuget locals all -clear               #Clear all caches
```

<span data-ttu-id="fb07a-110">Observe que o gerenciamento de cache é compatível atualmente somente com a linha de comando do NuGet e não com o Visual Studio, nem por meio do Console do Gerenciador de Pacotes.</span><span class="sxs-lookup"><span data-stu-id="fb07a-110">Note that managing the cache is presently supported only from the NuGet command line, and not within Visual Studio or through the Package Manager Console.</span></span> <span data-ttu-id="fb07a-111">Além disso, o gerenciamento de cache do 2.x não é compatível com o NuGet 3.6 e posterior.</span><span class="sxs-lookup"><span data-stu-id="fb07a-111">Also, managing the 2.x cache is not supported in NuGet 3.6 and later.</span></span>

<span data-ttu-id="fb07a-112">Os seguintes erros podem ocorrer ao usar `nuget locals`:</span><span class="sxs-lookup"><span data-stu-id="fb07a-112">The following errors can occur when using `nuget locals`:</span></span>

* <span data-ttu-id="fb07a-113">**Falha ao limpar os recursos locais: não é possível excluir um ou mais arquivos**</span><span class="sxs-lookup"><span data-stu-id="fb07a-113">**Clearing local resources failed: Unable to delete one or more files**</span></span>
* <span data-ttu-id="fb07a-114">**O diretório não está vazio**</span><span class="sxs-lookup"><span data-stu-id="fb07a-114">**The directory is not empty**</span></span>

<span data-ttu-id="fb07a-115">Eles indicam que você não tem permissão para excluir arquivos no cache ou que um ou mais arquivos no cache estão sendo usados por outro processo, o qual precisa ser fechado para que tais arquivos possam ser removidos.</span><span class="sxs-lookup"><span data-stu-id="fb07a-115">These indicate that you either do not have permission to delete files in the cache, or that one or more files in the cache are in use by another process, which must be closed before the those files can be removed.</span></span>