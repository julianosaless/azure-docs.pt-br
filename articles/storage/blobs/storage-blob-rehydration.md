---
title: Reidratar dados de blob da camada de arquivos
description: Reidrate os blobs do armazenamento de arquivos para acessar os dados.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 1265d018997f9540e14e83ab15a44e78f4f86fb1
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402654"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Reidratar dados de blob da camada de arquivos

Enquanto um blob estiver na camada de acesso aos arquivos, ele será considerado offline e não poderá ser lido nem alterado. Os metadados de blob permanecem online e disponíveis, permitindo que você liste o blob e as propriedades dele. A leitura e a alteração de dados de blob estão disponíveis somente com camadas online, como a quente ou a fria. Há duas opções para recuperar e acessar os dados armazenados na camada de acesso aos arquivos.

1. [Reidratar um blob arquivado em uma camada online](#rehydrate-an-archived-blob-to-an-online-tier): reidrate um blob de arquivos na camada quente ou na fria alterando a camada dele usando a operação [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier).
2. [Copiar um blob arquivado em uma camada online](#copy-an-archived-blob-to-an-online-tier): crie uma cópia de um blob de arquivos usando a operação [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob). Especifique um nome de blob diferente e uma camada de destino quente ou fria.

 Para obter mais informações sobre as camadas, confira [Armazenamento de Blobs do Azure: camadas de acesso quente, frio e aos arquivos](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Reidratar um blob arquivado em uma camada online

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Copiar um blob arquivado em uma camada online

Caso não deseje reidratar o blob de arquivos, você pode optar por realizar uma operação [Copy Blob](https://docs.microsoft.com/rest/api/storageservices/copy-blob). O blob original permanecerá inalterado em arquivos enquanto um blob for criado na camada online quente ou fria para você trabalhar. Na operação Copy Blob, também é possível definir a propriedade opcional *x-ms-rehydrate-priority* como Padrão ou Alta para especificar a prioridade sob a qual você deseja que a cópia de blob seja criada.

A cópia de um blob de arquivos pode levar horas para ser concluída, dependendo da prioridade de reidratação selecionada. Nos bastidores, a operação **Copy Blob** lê o blob de arquivos de origem para criar um blob online na camada de destino selecionada. É possível que o novo blob fique visível ao você listar os blobs. No entanto, os dados estão indisponíveis até que a leitura do blob de arquivos de origem seja concluída e que os dados sejam gravados no novo blob de destino online. O novo blob é uma cópia independente. Nenhuma modificação nem exclusão feita a ele afeta o blob de arquivos de origem.

> [!IMPORTANT]
> Não exclua o blob de origem até que a cópia seja concluída com êxito no destino. Caso o blob de origem seja excluído, é possível que o blob de destino não conclua a cópia e fique vazio. Você pode verificar o *x-ms-copy-status* para determinar o estado da operação de cópia.

Só é possível copiar os blobs de arquivos nas camadas de destino online dentro da mesma conta de armazenamento. Não há suporte para a cópia de um blob de arquivos em outro blob de arquivos. A tabela a seguir indica os recursos do CopyBlob.

|                                           | **Origem da camada quente**   | **Origem da camada fria** | **Origem da camada de arquivos**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Destino da camada quente**                  | Com suporte             | Com suporte            | Com suporte na mesma conta; reidratação pendente               |
| **Destino da camada fria**                 | Com suporte             | Com suporte            | Com suporte na mesma conta; reidratação pendente               |
| **Destino da camada de arquivos**              | Com suporte             | Com suporte            | Sem suporte         |

## <a name="pricing-and-billing"></a>Preços e cobrança

A reidratação de blobs de arquivos nas camadas quente ou fria é cobrada como operações de leitura e recuperação de dados. O uso da prioridade alta tem custos de operação e de recuperação de dados maiores em comparação à prioridade padrão. A reidratação de alta prioridade aparece como um item de linha separado na fatura. Se uma solicitação de alta prioridade para retornar um blob de arquivos de alguns gigabytes levar mais de cinco horas, você não será cobrado pela taxa de recuperação de alta prioridade. No entanto, as taxas de recuperação padrão ainda se aplicam, pois a reidratação foi priorizada em relação a outras solicitações.

A cópia de blobs de arquivos nas camadas quente ou fria é cobrada como operações de leitura e recuperação de dados. Uma operação de gravação é cobrada pela criação da cópia de blob. As tarifas de exclusão antecipada não se aplicam quando você copia em um blob online porque o blob de origem permanece inalterado na camada de arquivos. Se selecionados, os preços de recuperação de alta prioridade se aplicam.

Os blobs na camada de arquivos devem ser armazenados por um mínimo de 180 dias. A exclusão ou a reidratação de blobs arquivados antes de 180 dias incorrerá em tarifas de exclusão antecipada.

> [!NOTE]
> Para obter mais informações sobre o preço de blobs de blocos e de reidratação de dados, confira [Preço do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Para obter mais informações sobre os preços de transferência de dados de saída, confira [Detalhes do preço de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Cenários de início rápido

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Reidratar um blob de arquivos em uma camada online
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Entre no [portal do Azure](https://portal.azure.com).

1. No portal do Azure, pesquise e selecione **Todos os recursos**.

1. Selecione sua conta de armazenamento.

1. Selecione o contêiner e clique no blob.

1. Nas **Propriedades de blob**, selecione **Alterar camada**.

1. Selecione a camada de acesso **Quente** ou **Fria**. 

1. Selecione uma prioridade **Padrão** ou **Alta** de reidratação.

1. Selecione **Salvar** na parte inferior.

![Alterar camada da conta de armazenamento](media/storage-tiers/blob-access-tier.png)
![Verificar o estado de reidratação](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
É possível usar o script do PowerShell a seguir para alterar a camada de blob de um blob de arquivos. A variável `$rgName` deve ser inicializada com o nome do grupo de recursos. A variável `$accountName` deve ser inicializada com o nome da conta de armazenamento. A variável `$containerName` deve ser inicializada com o nome do contêiner. A variável `$blobName` deve ser inicializada com o nome do blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Copiar um blob de arquivos em um novo blob com uma camada online
É possível usar o script do PowerShell a seguir para copiar um blob de arquivos em um novo blob dentro da mesma conta de armazenamento. A variável `$rgName` deve ser inicializada com o nome do grupo de recursos. A variável `$accountName` deve ser inicializada com o nome da conta de armazenamento. As variáveis `$srcContainerName` e `$destContainerName` devem ser inicializadas com os nomes de contêiner. As variáveis `$srcBlobName` e `$destBlobName` devem ser inicializadas com os nomes de blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre as camadas de armazenamento de blobs](storage-blob-storage-tiers.md)
* [Verificar o preço de frequente, esporádica e de arquivo em contas de Armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)
* [Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)
* [Verificar os preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
