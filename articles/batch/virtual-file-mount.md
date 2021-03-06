---
title: Montar um sistema de arquivos virtual em um pool – lote do Azure | Microsoft Docs
description: Saiba como montar um sistema de arquivos virtual em um pool do lote.
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.openlocfilehash: 703b65f0a1571659d7be479776dd8fdf02d86731
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117022"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Montar um sistema de arquivos virtual em um pool do lote

O lote do Azure agora dá suporte à montagem de armazenamento em nuvem ou um sistema de arquivos externo em nós de computação do Windows ou Linux em seus pools do lote. Quando um nó de computação ingressa em um pool, o sistema de arquivos virtual é montado e tratado como uma unidade local nesse nó. Você pode montar sistemas de arquivos, como arquivos do Azure, armazenamento de BLOBs do Azure, NFS (sistema de arquivos de rede), incluindo um [cache avere vFXT](../avere-vfxt/avere-vfxt-overview.md)ou CIFS (Common Internet File System).

Neste artigo, você aprenderá a montar um sistema de arquivos virtual em um pool de nós de computação usando a [biblioteca de gerenciamento de lote para .net](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet).

> [!NOTE]
> A montagem de um sistema de arquivos virtual tem suporte em pools do lote criados em ou após 2019-08-19. Os pools do lote criados antes de 2019-08-19 não oferecem suporte a esse recurso.
> 
> As APIs para montar sistemas de arquivos em um nó de computação fazem parte da biblioteca [.net do lote](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet) .

## <a name="benefits-of-mounting-on-a-pool"></a>Benefícios da montagem em um pool

A montagem do sistema de arquivos no pool, em vez de permitir que as tarefas recuperem seus próprios dados de um grande conjunto de dados, torna mais fácil e mais eficiente para as tarefas acessar os dados necessários.

Considere um cenário com várias tarefas que exigem acesso a um conjunto comum de dados, como renderizar um filme. Cada tarefa renderiza um ou mais quadros por vez a partir dos arquivos de cena. Ao montar uma unidade que contém os arquivos de cena, é mais fácil para os nós de computação acessarem dados compartilhados. Além disso, o sistema de arquivos subjacente pode ser escolhido e dimensionado de forma independente com base no desempenho e na escala (taxa de transferência e IOPS) exigidos pelo número de nós de computação que acessam os dados simultaneamente. Por exemplo, um cache na memória distribuído [avere vFXT](../avere-vfxt/avere-vfxt-overview.md) pode ser usado para dar suporte a renderizações de escala de imagem de movimento grande com milhares de nós de renderização simultâneos, acessando dados de origem que residem localmente. Como alternativa, para dados que já residem no armazenamento de BLOBs baseado em nuvem, o [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) pode ser usado para montar esses dados como um sistema de arquivos local. O Blobfuse só está disponível em nós do Linux, no entanto, [os arquivos do Azure](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) fornecem um fluxo de trabalho semelhante e está disponível no Windows e no Linux.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Montar um sistema de arquivos virtual em um pool  

A montagem de um sistema de arquivos virtual em um pool torna o sistema de arquivos disponível para cada nó de computação no pool. O sistema de arquivos é configurado quando um nó de computação ingressa em um pool ou quando o nó é reiniciado ou a imagem é reiniciada.

Para montar um sistema de arquivos em um pool, crie `MountConfiguration` um objeto. Escolha o objeto que se adapta ao seu sistema de `AzureBlobFileSystemConfiguration`arquivos `AzureFileShareConfiguration`virtual `NfsMountConfiguration`:, `CifsMountConfiguration`, ou.

Todos os objetos de configuração de montagem precisam dos seguintes parâmetros de base. Algumas configurações de montagem têm parâmetros específicos para o sistema de arquivos que estão sendo usados, que são abordados em mais detalhes nos exemplos de código.

- **Nome da conta ou origem**: para montar um compartilhamento de arquivos virtual, você precisa do nome da conta de armazenamento ou de sua origem.
- **Caminho de montagem relativo ou origem**: o local do sistema de arquivos montado no nó de computação, em relação `fsmounts` ao diretório padrão acessível no nó `AZ_BATCH_NODE_MOUNTS_DIR`por meio do. O local exato varia dependendo do sistema operacional usado no nó. Por exemplo, o local físico em um nó Ubuntu é mapeado para `mnt\batch\tasks\fsmounts`e em um nó CentOS ao `mnt\resources\batch\tasks\fsmounts`qual está mapeado.
- Opções **de montagem ou opções de blobfuse**: essas opções descrevem parâmetros específicos para montar um sistema de arquivos.

Depois que `MountConfiguration` o objeto for criado, atribua o objeto à `MountConfigurationList` Propriedade ao criar o pool. O sistema de arquivos é montado quando um nó ingressa em um pool ou quando o nó é reiniciado ou a imagem é reiniciada.

Quando o sistema de arquivos é montado, é criada `AZ_BATCH_NODE_MOUNTS_DIR` uma variável de ambiente que aponta para o local dos sistemas de arquivos montados, bem como arquivos de log, que são úteis para a solução de problemas e a depuração. Os arquivos de log são explicados com mais detalhes na seção [diagnosticar erros de montagem](#diagnose-mount-errors) .  

> [!IMPORTANT]
> O número máximo de sistemas de arquivos montados em um pool é 10. Consulte [cotas e limites do serviço de lote](batch-quota-limit.md#other-limits) para obter detalhes e outros limites.

## <a name="examples"></a>Exemplos

Os exemplos de código a seguir demonstram a montagem de uma variedade de compartilhamentos de arquivos em um pool de nós de computação.

### <a name="azure-files-share"></a>Compartilhamento de arquivos do Azure

O arquivos do Azure é a oferta padrão do sistema de arquivos de nuvem do Azure. Para saber mais sobre como obter qualquer um dos parâmetros no exemplo de código de configuração de montagem, confira [usar um compartilhamento de arquivos do Azure](../storage/files/storage-how-to-use-files-windows.md).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Sistema de arquivos de blob do Azure

Outra opção é usar o armazenamento de BLOBs do Azure via [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md). A montagem de um sistema de arquivos `AccountKey` de `SasKey` blob requer um ou para sua conta de armazenamento. Para obter informações sobre como obter essas chaves, consulte [gerenciar chaves de acesso da conta de armazenamento](../storage/common/storage-account-keys-manage.md)ou [usando SAS (assinaturas de acesso compartilhado)](../storage/common/storage-dotnet-shared-access-signature-part-1.md). Para obter mais informações sobre como usar blobfuse, consulte as [perguntas frequentes sobre solução de problemas](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)do blobfuse. Para obter acesso padrão ao diretório montado blobfuse, execute a tarefa como **administrador**. O Blobfuse monta o diretório no espaço do usuário e, na criação do pool, é montado como raiz. No Linux, todas as tarefas de **administrador** são raiz. Todas as opções para o módulo fusível são descritas na [página de referência fusível](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html).

Além do guia de solução de problemas, os problemas do GitHub no repositório blobfuse são uma maneira útil de verificar os problemas e as resoluções atuais do blobfuse. Para obter mais informações, consulte [blobfuse issues](https://github.com/Azure/azure-storage-fuse/issues).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>NFS

NFS (Network File Systems) também pode ser montado em nós de pool, permitindo que os sistemas de arquivos tradicionais sejam acessados facilmente por nós do lote do Azure. Isso pode ser um único servidor NFS implantado na nuvem ou um servidor NFS local acessado em uma rede virtual. Como alternativa, aproveite a solução de cache [avere vFXT](../avere-vfxt/avere-vfxt-overview.md) distribuída na memória, que fornece conectividade direta com o armazenamento local, lendo dados sob demanda em seu cache e fornece alto desempenho e escala para nós de computação baseados em nuvem.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Sistema de arquivos de Internet comum

CIFS (Common Internet File System) também pode ser montado em nós de pool, permitindo que os sistemas de arquivos tradicionais sejam acessados facilmente por nós do lote do Azure. O CIFS é um protocolo de compartilhamento de arquivos que fornece um mecanismo de plataforma cruzada e aberta para solicitar serviços e arquivos de servidor de rede. O CIFS é baseado na versão aprimorada do protocolo SMB para Internet e compartilhamento de arquivos de intranet e é usado para montar sistemas de arquivos externos em nós do Windows. Para saber mais sobre o SMB, consulte [servidor de arquivos e SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview).

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Diagnosticar erros de montagem

Se uma configuração de montagem falhar, o nó de computação no pool falhará e o estado do nó se tornará inutilizável. Para diagnosticar uma falha de configuração de montagem [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) , inspecione a propriedade para obter detalhes sobre o erro.

Para obter os arquivos de log para depuração, use [OutputFiles](batch-task-output-files.md) para carregar `*.log` os arquivos. Os `*.log` arquivos contêm informações sobre a montagem do sistema de arquivos `AZ_BATCH_NODE_MOUNTS_DIR` no local. Os arquivos de log de montagem têm `<type>-<mountDirOrDrive>.log` o formato: para cada montagem. Por exemplo, uma `cifs` montagem em um diretório de montagem `test` chamado terá um arquivo de log de montagem `cifs-test.log`chamado:.

## <a name="supported-skus"></a>SKUs com suporte

| Publicador | Oferta | SKU | Compartilhamento de arquivos do Azure | Blobfuse | Montagem de NFS | Montagem de CIFS |
|---|---|---|---|---|---|---|
| lote | rendering-centos73 | renderização | :heavy_check_mark: <br>Observação: compatível com CentOS 7,7</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canônico | UbuntuServer | 16, 4-LTS, 18, 4-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>Observação: compatível com o CentOS 7,4. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>Observação: dá suporte ao armazenamento de A_8 ou 9</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7,4, 7,3, 7,1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais detalhes sobre como montar um compartilhamento de arquivos do Azure com [Windows](../storage/files/storage-how-to-use-files-windows.md) ou [Linux](../storage/files/storage-how-to-use-files-linux.md).
- Saiba mais sobre como usar e montar sistemas de arquivos virtuais [blobfuse](https://github.com/Azure/azure-storage-fuse) .
- Consulte [visão geral do sistema de arquivos de rede](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview) para saber mais sobre NFS e seus aplicativos.
- Consulte [Microsoft SMB Protocol and CIFS Protocol Overview](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) para saber mais sobre o CIFS.
