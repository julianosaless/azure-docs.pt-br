---
title: Criar um modelo do construtor de imagens do Azure (visualização)
description: Saiba como criar um modelo para usar com o construtor de imagem do Azure.
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: cynthn
ms.openlocfilehash: c13ace67f18b619d5ad86106ecb648db722be9fa
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792438"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Versão prévia: criar um modelo do construtor de imagens do Azure 

O construtor de imagens do Azure usa um arquivo. JSON para passar informações para o serviço do construtor de imagem. Neste artigo, vamos abordar as seções do arquivo JSON, para que você possa criar o seu próprio. Para ver exemplos de arquivos. JSON completos, consulte o [GitHub do Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Este é o formato de modelo básico:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "name": "<vnetName>",
                "subnetName": "<subnetName>",
                "resourceGroupName": "<vnetRgName>"
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Tipo e versão da API

O `type` é o tipo de recurso, que deve `"Microsoft.VirtualMachineImages/imageTemplates"`ser. O `apiVersion` será alterado ao longo do tempo conforme a API é alterada, `"2019-05-01-preview"` mas deve ser para visualização.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Local

O local é a região em que a imagem personalizada será criada. Para a visualização do Image Builder, há suporte para as seguintes regiões:

- Leste dos EUA
- Leste dos EUA 2
- Centro-Oeste dos EUA
- Oeste dos EUA
- Oeste dos EUA 2
- Norte da Europa
- Europa Ocidental


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
Por padrão, o construtor de imagem usará uma VM de Build "Standard_D1_v2", você poderá substituir isso, por exemplo, se desejar personalizar uma imagem para uma VM GPU, você precisará de um tamanho de VM de GPU. Isso é opcional.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Por padrão, o Image Builder não alterará o tamanho da imagem, ele usará o tamanho da imagem de origem. Você pode aumentar o tamanho do disco do sistema operacional (Win e Linux), isso é opcional, e o valor 0 significa deixar o mesmo tamanho da imagem de origem. 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Se você não especificar propriedades de VNET, o construtor de imagem criará sua própria VNET, IP público e NSG. O IP público é usado para que o serviço se comunique com a VM de compilação. no entanto, se você não quiser um IP público ou desejar que o construtor de imagens tenha acesso aos recursos de VNET existentes, como servidores de configuração (DSC, chefe, Puppet, Ansible), compartilhamentos de arquivos etc., você poderá especificar uma VNET. Para obter mais informações, consulte a [documentação de rede](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder), isso é opcional.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Marcas

Esses são pares de chave/valor que você pode especificar para a imagem gerada.

## <a name="depends-on-optional"></a>Depende de (opcional)

Essa seção opcional pode ser usada para garantir que as dependências sejam concluídas antes de continuar. 

```json
    "dependsOn": [],
```

Para obter mais informações, consulte [definir dependências de recurso](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identidade
Por padrão, o Image Builder dá suporte ao uso de scripts ou à cópia de arquivos de vários locais, como o GitHub e o armazenamento do Azure. Para usá-los, eles devem ser acessíveis publicamente.

Você também pode usar uma identidade gerenciada atribuída pelo usuário do Azure, definida por você, para permitir que o construtor de imagens acesse o armazenamento do Azure, desde que a identidade tenha recebido um mínimo de ' leitor de dados de blob de armazenamento ' na conta de armazenamento do Azure. Isso significa que você não precisa tornar os blobs de armazenamento acessíveis externamente ou configurar tokens SAS.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Para obter um exemplo completo, consulte [usar uma identidade gerenciada atribuída pelo usuário do Azure para acessar arquivos no armazenamento do Azure](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Suporte ao construtor de imagem para uma identidade atribuída pelo usuário: • dá suporte apenas a uma única identidade • não dá suporte a nomes de domínio personalizados

Para saber mais, confira [o que são identidades gerenciadas para recursos do Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Para obter mais informações sobre como implantar esse recurso, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando CLI do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Propriedades: origem

A `source` seção contém informações sobre a imagem de origem que será usada pelo Image Builder.

A API requer um ' SourceType ' que define a origem para a compilação da imagem, atualmente há três tipos:
- PlatformImage-indica que a imagem de origem é uma imagem do Marketplace.
- ManagedImage-use isso ao iniciar em uma imagem gerenciada normal.
- SharedImageVersion-isso é usado quando você está usando uma versão de imagem em uma galeria de imagens compartilhada como a origem.

### <a name="iso-source"></a>Origem ISO
Estamos preterindo essa funcionalidade do Image Builder, já que agora [o RHEL traz suas próprias imagens de assinatura](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos), examine os cronogramas abaixo:
    * 31 de março de 2020-modelos de imagem com fontes ISO RHEL agora serão mais aceitos pelo provedor de recursos.
    * 30 de abril de 2020-modelos de imagem que contêm fontes ISO de RHEL não serão mais processados.

### <a name="platformimage-source"></a>Origem do PlatformImage 
O construtor de imagem do Azure dá suporte ao Windows Server e ao cliente e às imagens do Azure Marketplace do Linux, consulte [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) para obter a lista completa. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


As propriedades aqui são as mesmas usadas para criar VMs, usando AZ CLI, execute o seguinte para obter as propriedades: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Você pode usar ' Latest ' na versão, a versão é avaliada quando a criação da imagem ocorre, não quando o modelo é enviado. Se você usar essa funcionalidade com o destino da Galeria de imagens compartilhadas, poderá evitar reenviar o modelo e executar novamente a compilação da imagem em intervalos, para que suas imagens sejam recriadas a partir das imagens mais recentes.

### <a name="managedimage-source"></a>Origem do ManagedImage

Define a imagem de origem como uma imagem gerenciada existente de um VHD ou VM generalizado. A imagem gerenciada de origem deve ser de um sistema operacional com suporte e estar na mesma região que o seu modelo do Azure Image Builder. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

O `imageId` deve ser o ResourceId da imagem gerenciada. Use `az image list` para listar imagens disponíveis.


### <a name="sharedimageversion-source"></a>Origem do SharedImageVersion
Define a imagem de origem como uma versão de imagem existente em uma galeria de imagens compartilhada. A versão da imagem deve ser de um sistema operacional com suporte e a imagem deve ser replicada na mesma região que o seu modelo do construtor de imagem do Azure. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

O `imageVersionId` deve ser o ResourceId da versão da imagem. Use a [lista de versão de imagem AZ SIG](/cli/azure/sig/image-version#az-sig-image-version-list) para listar versões de imagem.

## <a name="properties-buildtimeoutinminutes"></a>Propriedades: buildTimeoutInMinutes

Por padrão, o construtor de imagem será executado por 240 minutos. Depois disso, ele irá expirar e parar, independentemente de a compilação da imagem ser concluída ou não. Se o tempo limite for atingido, você verá um erro semelhante a este:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Se você não especificar um valor de buildTimeoutInMinutes ou defini-lo como 0, isso usará o valor padrão. Você pode aumentar ou diminuir o valor, até o máximo de 960mins (16hrs). Para o Windows, não recomendamos definir isso abaixo de 60 minutos. Se você achar que está atingindo o tempo limite, examine os [logs](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)para ver se a etapa de personalização está aguardando algo como entrada do usuário. 

Se você achar que precisa de mais tempo para que as personalizações sejam concluídas, defina isso para o que você acha que precisa, com uma pequena sobrecarga. No entanto, não defina-o muito alto, pois talvez seja necessário aguardar até que ele fique em tempo limite antes de ver um erro. 


## <a name="properties-customize"></a>Propriedades: personalizar

O Image Builder dá suporte a vários ' customers '. Os personalizadores são funções que são usadas para personalizar a imagem, como a execução de scripts ou a reinicialização de servidores. 

Ao usar `customize`: 
- Você pode usar vários personalizadores, mas eles devem ter um exclusivo `name`.
- Os personalizadores são executados na ordem especificada no modelo.
- Se um personalizador falhar, o componente de personalização inteiro falhará e relatará um erro.
- É altamente recomendável que você teste o script cuidadosamente antes de usá-lo em um modelo. A depuração do script em sua própria VM será mais fácil.
- Não coloque dados confidenciais nos scripts. 
- Os locais de script precisam ser acessíveis publicamente, a menos que você esteja usando o [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
A seção Personalizar é uma matriz. O construtor de imagens do Azure será executado por meio dos personalizadores em ordem sequencial. Qualquer falha em qualquer personalizador irá falhar o processo de compilação. 
 
 
### <a name="shell-customizer"></a>Personalizador de Shell

O personalizador de shell dá suporte a scripts de Shell em execução, eles devem ser acessíveis publicamente para que o IB possa acessá-los.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

Suporte do so: Linux 
 
Personalizar propriedades:

- **tipo** – Shell 
- **nome** -nome para acompanhar a personalização 
- **scriptUri** -URI para o local do arquivo 
- matriz **embutida** de comandos do Shell, separados por vírgulas.
- **sha256Checksum** -valor da soma de verificação SHA256 do arquivo, você o gera localmente e, em seguida, o Image Builder irá somar e validar.
    * Para gerar o sha256Checksum, usando um terminal no Mac/Linux, execute:`sha256sum <fileName>`


Para comandos a serem executados com privilégios de superusuário, eles devem ser prefixados com `sudo`.

> [!NOTE]
> Ao executar o personalizador de shell com a fonte ISO do RHEL, você precisa garantir que seu primeiro Shell de personalização manipule o registro com um servidor de direitos do Red Hat antes que ocorra qualquer personalização. Depois que a personalização for concluída, o script deverá cancelar o registro com o servidor de direitos.

### <a name="windows-restart-customizer"></a>Personalizador de reinicialização do Windows 
O personalizador de reinicialização permite reiniciar uma VM do Windows e aguardar que ela volte a ficar online, permitindo que você instale o software que exige uma reinicialização.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

Suporte do so: Windows
 
Personalizar propriedades:
- **Tipo**: WindowsRestart
- **restartCommand** -comando para executar a reinicialização (opcional). O padrão é `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – comando para verificar se a reinicialização foi bem-sucedida (opcional). 
- **restartTimeout** -tempo limite de reinicialização especificado como uma cadeia de magnitude e unidade. Por exemplo, `5m` (5 minutos) ou `2h` (2 horas). O padrão é: ' 5 min '

### <a name="linux-restart"></a>Reinicialização do Linux  
No entanto, não há nenhum personalizador de reinicialização do Linux, no entanto, se você estiver instalando drivers ou componentes que exigem uma reinicialização, você pode instalá-los e invocar uma reinicialização usando o personalizador de Shell, há um tempo limite de SSH 20min para a VM de compilação.

### <a name="powershell-customizer"></a>Personalizador do PowerShell 
O personalizador de shell dá suporte à execução de scripts do PowerShell e ao comando embutido, os scripts devem ser publicamente acessíveis para que o IB possa acessá-los.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

Suporte do so: Windows e Linux

Personalizar propriedades:

- **tipo** – PowerShell.
- **scriptUri** -URI para o local do arquivo de script do PowerShell. 
- **embutido** – comandos embutidos a serem executados, separados por vírgulas.
- **validExitCodes** – códigos opcionais válidos que podem ser retornados do comando de script/embutido, isso evitará a falha relatada do comando script/embutido.
- **runElevated** – opcional, booliano, suporte para a execução de comandos e scripts com permissões elevadas.
- **sha256Checksum** -valor da soma de verificação SHA256 do arquivo, você o gera localmente e, em seguida, o Image Builder irá somar e validar.
    * Para gerar o sha256Checksum, usando um PowerShell no Windows [Get-hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Personalizador de arquivo

O personalizador de arquivo permite que o construtor de imagens Baixe um arquivo de um GitHub ou armazenamento do Azure. Se você tiver um pipeline de compilação de imagem que se baseia em artefatos de compilação, você pode definir o personalizador de arquivo para baixar do compartilhamento de compilação e mover os artefatos para a imagem.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

Suporte do so: Linux e Windows 

Propriedades do personalizador de arquivo:

- **SourceUri** – um ponto de extremidade de armazenamento acessível, que pode ser o GitHub ou o armazenamento do Azure. Você só pode baixar um arquivo, não um diretório inteiro. Se você precisar baixar um diretório, use um arquivo compactado e descompacte-o usando os personalizadores do Shell ou do PowerShell. 
- **destino** – este é o caminho de destino completo e o nome do arquivo. Qualquer caminho e subdiretórios referenciados devem existir, use os personalizados do Shell ou do PowerShell para configurá-los com antecedência. Você pode usar os personalizadores de script para criar o caminho. 

Isso é suportado por diretórios do Windows e caminhos do Linux, mas há algumas diferenças: 
- SO Linux – o único criador de imagem de caminho pode gravar em é/tmp.
- Windows – nenhuma restrição de caminho, mas o caminho deve existir.
 
 
Se houver um erro ao tentar baixar o arquivo, ou colocá-lo em um diretório especificado, a etapa de personalização falhará e isso estará no arquivo customization. log.

> [!NOTE]
> O personalizador de arquivos é adequado apenas para downloads de arquivos pequenos, < 20 MB. Para downloads de arquivos maiores, use um script ou comando embutido, o código de uso para baixar arquivos, `wget` como `curl`Linux ou, `Invoke-WebRequest`Windows,.

Os arquivos no Personalizador de arquivo podem ser baixados do armazenamento do Azure usando o [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="windows-update-customizer"></a>Personalizador de Windows Update
Esse personalizador se baseia na [comunidade Windows Update provisioner](https://packer.io/docs/provisioners/community-supported.html) para o Packer, que é um projeto de software livre mantido pela Comunidade do Packer. A Microsoft testa e valida o provisionamento com o serviço do Image Builder e dará suporte à investigação de problemas com ele e trabalha para resolver problemas, no entanto, o projeto de software livre não é oficialmente suportado pela Microsoft. Para obter a documentação detalhada e obter ajuda com o provisionamento do Windows Update, consulte o repositório do projeto.
 
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
Suporte do so: Windows

Personalizar propriedades:
- **tipo** – windowsupdate.
- **critériosdepesquisa** – opcional, define que tipo de atualizações são instaladas (recomendado, importante etc.), BrowseOnly = 0 e IsInstalled = 0 (recomendado) é o padrão.
- **filtros** – opcional, permite que você especifique um filtro para incluir ou excluir atualizações.
- **updateLimit** – opcional, define quantas atualizações podem ser instaladas, o padrão é 1000.
 
 

### <a name="generalize"></a>Generalizar 
Por padrão, o construtor de imagens do Azure também executará o código de ' desprovisionamento ' no final de cada fase de personalização de imagem, para ' generalizar ' a imagem. Generalizar é um processo em que a imagem é configurada para que possa ser reutilizada para criar várias VMs. Para VMs do Windows, o construtor de imagem do Azure usa Sysprep. Para o Linux, o Azure Image Builder executa ' waagent-deprovision '. 

Os comandos que os usuários do construtor de imagens para generalizar podem não ser adequados para todas as situações, portanto, o construtor de imagens do Azure permitirá que você personalize esse comando, se necessário. 

Se você estiver migrando a personalização existente e estiver usando diferentes comandos Sysprep/waagent, poderá usar os comandos genéricos do Image Builder e, se a criação da VM falhar, use seus próprios comandos Sysprep ou waagent.

Se o construtor de imagem do Azure criar uma imagem personalizada do Windows com êxito e você criar uma VM a partir dela, descubra que a criação da VM falha ou não é concluída com êxito. você precisará examinar a documentação do Sysprep do Windows Server ou gerar uma solicitação de suporte com a equipe de suporte do Windows Server Sysprep Customer Services, que pode solucionar problemas e recomendar o uso correto do Sysprep


#### <a name="default-sysprep-command"></a>Comando Sysprep padrão
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Comando padrão de desprovisionamento do Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Substituindo os comandos
Para substituir os comandos, use os provisionadores do script PowerShell ou Shell para criar os arquivos de comando com o nome exato do arquivo e coloque-os nos diretórios corretos:

* Windows: c:\DeprovisioningScript.ps1
* Linux:/tmp/DeprovisioningScript.sh

O Image Builder lerá esses comandos, eles serão gravados nos logs do AIB, ' customization. log '. Consulte [solução de problemas](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) de como coletar logs.
 
## <a name="properties-distribute"></a>Propriedades: distribuir

O construtor de imagem do Azure dá suporte a três destinos de distribuição: 

- **managedImage** -imagem gerenciada.
- **sharedImage** -Galeria de imagens compartilhada.
- **VHD** -VHD em uma conta de armazenamento.

Você pode distribuir uma imagem para ambos os tipos de destino na mesma configuração, consulte [exemplos](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Como você pode ter mais de um destino para distribuir, o Image Builder mantém um estado para cada destino de distribuição que pode ser acessado consultando o `runOutputName`.  O `runOutputName` é um objeto que você pode consultar após a distribuição para obter informações sobre essa distribuição. Por exemplo, você pode consultar o local do VHD ou as regiões nas quais a versão da imagem foi replicada ou a versão de imagem SIG criada. Essa é uma propriedade de cada destino de distribuição. O `runOutputName` deve ser exclusivo para cada destino de distribuição. Aqui está um exemplo, consultando uma distribuição da Galeria de imagens compartilhadas:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
```

Saída:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Distribuir: managedImage

A saída da imagem será um recurso de imagem gerenciada.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Distribuir Propriedades:
- **tipo** – managedImage 
- **imageid** – ID de recurso da imagem de destino, formato esperado:\</subscriptions/SubscriptionId>\</resourcegroups/destinationResourceGroupName>\</Providers/Microsoft.Compute/images/ImageName>
- **local** -local da imagem gerenciada.  
- **runOutputName** – nome exclusivo para identificar a distribuição.  
- **artifactTags** -marcas de par de valor de chave especificado pelo usuário opcional.
 
 
> [!NOTE]
> O grupo de recursos de destino deve existir.
> Se você quiser que a imagem seja distribuída para uma região diferente, ela aumentará o tempo de implantação. 

### <a name="distribute-sharedimage"></a>Distribuir: sharedImage 
A Galeria de imagens compartilhadas do Azure é um novo serviço de gerenciamento de imagens que permite o gerenciamento de replicação de região de imagem, controle de versão e compartilhamento de imagens personalizadas. O construtor de imagens do Azure dá suporte à distribuição com esse serviço, para que você possa distribuir imagens para regiões com suporte pelas galerias de imagens compartilhadas. 
 
Uma galeria de imagens compartilhada é composta de: 
 
- Galeria-contêiner para várias imagens compartilhadas. Uma galeria é implantada em uma região.
- Definições de imagem – um agrupamento conceitual para imagens. 
- Versões de imagem-esse é um tipo de imagem usado para implantar uma VM ou um conjunto de dimensionamento. As versões de imagem podem ser replicadas para outras regiões em que as VMs precisam ser implantadas.
 
Antes de distribuir para a Galeria de imagens, você deve criar uma galeria e uma definição de imagem, ver [imagens compartilhadas](shared-images.md). 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Distribuir Propriedades para galerias de imagens compartilhadas:

- **tipo** -sharedImage  
- **galleryImageId** – ID da Galeria de imagens compartilhada. O formato é:/subscriptions/\<subscriptionid>/resourcegroups/\<resourceGroupName>/Providers/Microsoft.Compute/Galleries/\<sharedImageGalleryName>/images/\<imageGalleryName>.
- **runOutputName** – nome exclusivo para identificar a distribuição.  
- **artifactTags** -marcas de par de valor de chave especificado pelo usuário opcional.
- **replicationRegions** -matriz de regiões para replicação. Uma das regiões deve ser a região onde a galeria é implantada.
 
> [!NOTE]
> Você pode usar o construtor de imagem do Azure em uma região diferente para a Galeria, mas o serviço do construtor de imagens do Azure precisará transferir a imagem entre os datacenters e isso levará mais tempo. O Image Builder fará a versão da imagem automaticamente, com base em um inteiro monotônico, você não poderá especificá-la no momento. 

### <a name="distribute-vhd"></a>Distribuir: VHD  
Você pode gerar uma saída para um VHD. Em seguida, você pode copiar o VHD e usá-lo para publicar no Azure MarketPlace ou usar com Azure Stack.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
Suporte do so: Windows e Linux

Distribuir parâmetros de VHD:

- **tipo** -VHD.
- **runOutputName** – nome exclusivo para identificar a distribuição.  
- **marcas** – marcas de par de valor de chave especificado pelo usuário opcional.
 
O construtor de imagens do Azure não permite que o usuário especifique um local de conta de armazenamento, mas você pode consultar `runOutputs` o status do para obter o local.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> Depois que o VHD tiver sido criado, copie-o para um local diferente, assim que possível. O VHD é armazenado em uma conta de armazenamento no grupo de recursos temporário criado quando o modelo de imagem é enviado para o serviço do construtor de imagem do Azure. Se você excluir o modelo de imagem, o VHD será perdido. 
 
## <a name="next-steps"></a>Próximas etapas

Há arquivos. JSON de exemplo para diferentes cenários no [GitHub do Azure Image Builder](https://github.com/danielsollondon/azvmimagebuilder).
 
