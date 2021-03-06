---
title: Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba mais sobre as limitações e os problemas conhecidos do Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: b02835ae3a1d7fed52f2cdb4ab25aa74ba66e8c3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119885"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Problemas conhecidos com o Azure Data Lake Storage Gen2 | Microsoft Docs

Este artigo descreve as limitações e os problemas conhecidos do Azure Data Lake Storage Gen2.

## <a name="supported-blob-storage-features"></a>Recursos do Armazenamento de Blobs compatíveis

Um número crescente de recursos de Armazenamento de Blobs agora funciona com contas que têm um namespace hierárquico. Para obter uma lista completa, confira [Recursos do Armazenamento de Blobs disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrações de serviço do Azure com suporte

O Azure Data Lake Storage Gen2 é compatível com vários serviços do Azure que você pode usar para ingerir dados, executar análises e criar representações visuais. Para obter uma lista de serviços do Azure com suporte, confira [Serviços do Azure que dão suporte ao Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

Confira [Serviços do Azure compatíveis com o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Plataformas de software livre com suporte

Várias plataformas de software livre suportam o Data Lake Storage Gen2. Para obter uma lista completa, confira [Plataformas de software livre que dão suporte ao Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

Confira [Plataformas de software livre compatíveis com o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>APIs de armazenamento de Blob

APIs de Blob e APIs do Data Lake Storage Gen2 podem operar nos mesmos dados.

Esta seção descreve problemas e limitações com o uso de APIs de Blob e APIs do Data Lake Storage Gen2 para operar nos mesmos dados.

* Você não pode usar as APIs de Blob e as APIs do Data Lake Storage para gravar na mesma instância de um arquivo. Se você gravar em um arquivo usando APIs Data Lake Storage Gen2, os blocos desse arquivo não estarão visíveis para chamadas para a API de Blob [Obter Lista de Blocos](https://docs.microsoft.com/rest/api/storageservices/get-block-list). Você pode substituir um arquivo usando as APIs do Data Lake Storage Gen2 ou as APIs de Blob. Isso não afetará as propriedades do arquivo.

* Quando você usar a operação [Listar Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) sem especificar um delimitador, os resultados incluirão diretórios e blobs. Se você optar por usar um delimitador, use apenas uma barra (`/`). Esse é o único delimitador compatível.

* Se você usar a API [Excluir Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) para excluir um diretório, esse diretório será excluído somente se estiver vazio. Isso significa que você não pode usar a API de Blob para excluir diretórios recursivamente.

Estas APIs REST de Blob não são compatíveis:

* [Colocar Blob (Página)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Colocar Página](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [OBter Intervalos de Página](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Blob de Cópia Incremental](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Colocar Página da URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Colocar Blob (Acrescentar)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Acrescentar Bloco](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Acrescentar Bloco da URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Discos de VM não gerenciados não são compatíveis com contas que têm um namespace hierárquico. Se você desejar habilitar um namespace hierárquico em uma conta de armazenamento, coloque os discos de VM não gerenciados em uma conta de armazenamento que não tenha o recurso de namespace hierárquico habilitado.

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks-powershell-and-azure-cli"></a>Suporte ao sistema de arquivos em SDKs, PowerShell e CLI do Azure

- As operações get e set da ACL não são recursivas no momento.


## <a name="lifecycle-management-policies"></a>Políticas de gerenciamento do ciclo de vida

A exclusão de instantâneos de blob ainda não é uma operação compatível. 

## <a name="archive-tier"></a>Camada de arquivo

Atualmente, há um bug que afeta a camada de acesso aos arquivos.

## <a name="blobfuse"></a>Blobfuse

O Blobfuse não é compatível.

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

Use apenas a versão mais recente do AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). As versões anteriores do AzCopy, como AzCopy v8.1, não são compatíveis.

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Gerenciador de Armazenamento do Azure

Use somente versões `1.6.0` ou posterior.

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Gerenciador de Armazenamento no portal do Azure

As ACLs ainda não são compatíveis.

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>Aplicativos de terceiros

Aplicativos de terceiros que usam APIs REST para funcionar continuarão a funcionar se você usá-los com o Data Lake Storage Gen2. Aplicativos que chamam APIs de Blob provavelmente funcionarão.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>As ACLs (listas de controle de acesso) e acesso de leitura anônimo

Se o [acesso de leitura anônimo](storage-manage-access-to-resources.md) tiver sido concedido a um contêiner, as ACLs não terão nenhum efeito nesse contêiner nem nos arquivos desse contêiner.

## <a name="premium-performance-block-blob-storage-accounts"></a>Contas de armazenamento de blobs de blocos de desempenho Premium

### <a name="diagnostic-logs"></a>Logs de diagnóstico

Os logs de diagnóstico ainda não podem ser habilitados usando o portal do Azure. Você pode habilitá-los usando o PowerShell. Por exemplo:

```powershell
#To login
Connect-AzAccount

#Set default block blob storage account.
Set-AzCurrentStorageAccount -Name premiumGen2Account -ResourceGroupName PremiumGen2Group

#Enable logging
Set-AzStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays 14
```

### <a name="lifecycle-management-policies"></a>Políticas de gerenciamento do ciclo de vida

- Políticas de gerenciamento de ciclo de vida ainda não são compatíveis com contas de armazenamento de blobs de blocos Premium. 

- Os dados não podem ser movidos da camada Premium para as camadas inferiores. 

- A ação **Excluir Blob** não é compatível no momento. 

### <a name="hdinsight-support"></a>Suporte do HDInsight

Ao criar um cluster n do HDInsight, você ainda não pode selecionar uma conta de armazenamento de blobs de blocos que tem o recurso de namespace hierárquico habilitado nela. No entanto, você pode anexar a conta ao cluster depois de criá-la.

### <a name="dremio-support"></a>Suporte do Dremio

O Dremio ainda não se conecta a uma conta de armazenamento de blobs de blocos que tem o recurso de namespace hierárquico habilitado nela. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Driver do WASB (Windows Azure Storage Blob) (não compatível com o Data Lake Storage Gen2)

Atualmente, o driver WASB, que foi projetado para funcionar apenas com a API de Blob, encontra problemas em alguns cenários comuns. Especificamente, quando ele é um cliente para uma conta de armazenamento habilitada para namespace hierárquico. O acesso de vários protocolos no Data Lake Storage não atenuará esses problemas. 

Por enquanto (e, provavelmente, no futuro próximo), não daremos suporte a clientes que usam o driver WASB como um cliente para uma conta de armazenamento habilitada para namespace hierárquico. Em vez disso, recomendamos que você opte por usar o driver do [ABFS (Azure Blob File System)](data-lake-storage-abfs-driver.md) em seu ambiente do Hadoop. Se você estiver tentando migrar de um ambiente local do Hadoop com uma versão anterior à Hadoop branch-3, abra um tíquete de suporte do Azure para que possamos entrar em contato com você e seguir o melhor rumo para você e sua organização.
