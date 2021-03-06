---
title: Como preparar um aplicativo Spring Java para implantação no Azure Spring Cloud
description: Neste tópico, você prepara um aplicativo Spring Java para implantação no Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 16cee333d52765755b732c4de4dd8a6e092a130d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731174"
---
# <a name="prepare-a-java-spring-application-for-deployment-in-azure-spring-cloud"></a>Preparar um aplicativo Spring Java para implantação no Azure Spring Cloud

Este tópico mostra como preparar um aplicativo Spring Java existente para implantação no Azure Spring Cloud. Se configurado corretamente, o Azure Spring Cloud fornecerá serviços robustos para monitorar, dimensionar e atualizar seu aplicativo Java Spring Cloud.

Outros exemplos explicam como implantar um aplicativo no Azure Spring Cloud quando o arquivo POM é configurado. 
* [Iniciar o aplicativo usando o portal do Azure](spring-cloud-quickstart-launch-app-portal.md)
* [Iniciar o aplicativo usando a CLI do Azure](spring-cloud-quickstart-launch-app-cli.md)

Este artigo explica as dependências necessárias e como adicioná-las ao arquivo POM.

## <a name="java-runtime-version"></a>Versão do Java Runtime

Somente aplicativos Spring/Java podem ser executados no Azure Spring Cloud.

O Azure Spring Cloud dá suporte ao Java 8 e ao Java 11. O ambiente de hospedagem contém a última versão do Azul Zulu OpenJDK para Azure. Para obter mais informações sobre o Azul Zulu OpenJDK para Azure, confira [Instalar o JDK](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Versões Spring Boot e Spring Cloud

Para preparar um aplicativo Spring boot existente para implantação no Azure Spring Cloud, inclua as dependências Spring Boot e Spring Cloud no arquivo POM do aplicativo, conforme mostrado nas seções a seguir.

O Azure Spring Cloud é compatível apenas com aplicativos Spring boot versão 2.1 ou 2.2. A seguinte tabela lista as combinações do Spring Boot e do Spring Cloud compatíveis:

Versão do Spring Boot | Versão do Spring Cloud
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton.RELEASE

### <a name="dependencies-for-spring-boot-version-21"></a>Dependências do Spring Boot versão 2.1

Para o Spring Boot versão 2.1, adicione as dependências a seguir ao arquivo POM do aplicativo.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.SR4</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Dependências do Spring Boot versão 2.2

Para o Spring Boot versão 2.2, adicione as dependências a seguir ao arquivo POM do aplicativo.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

## <a name="azure-spring-cloud-client-dependency"></a>Dependência de cliente do Azure Spring Cloud

O Azure Spring Cloud hospeda e gerencia os componentes do Spring Cloud. Os componentes incluem o Registro de Serviço do Spring Cloud e o Servidor de Configuração do Spring Cloud. Inclua a biblioteca de clientes do Azure Spring Cloud em suas dependências para permitir a comunicação com sua instância de serviço do Azure Spring Cloud.

A tabela a seguir lista as versões corretas do Azure Spring Cloud para o aplicativo que usa o Spring Boot e o Spring Cloud.

Versão do Spring Boot | Versão do Spring Cloud | Versão do Azure Spring Cloud
---|---|---
2.1 | Greenwich.RELEASE | 2.1
2.2 | Hoxton.RELEASE | 2.2

Inclua uma das dependências a seguir no arquivo pom.xml. Selecione a dependência cuja versão do Azure Spring Cloud corresponde à sua.

### <a name="dependency-for-azure-spring-cloud-version-21"></a>Dependência do Azure Spring Cloud versão 2.1

Para o Spring Boot versão 2.1, adicione a dependência a seguir ao arquivo POM do aplicativo.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.1</version>
</dependency>
```

### <a name="dependency-for-azure-spring-cloud-version-22"></a>Dependência do Azure Spring Cloud versão 2.2

Para o Spring Boot versão 2.2, adicione a dependência a seguir ao arquivo POM do aplicativo.

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.2.0</version>
</dependency>
```

## <a name="other-required-dependencies"></a>Outras dependências necessárias

Para habilitar os recursos internos do Azure Spring Cloud, seu aplicativo deve incluir as seguintes dependências. Essa inclusão garante que o aplicativo seja configurado corretamente com cada componente.

### <a name="enablediscoveryclient-annotation"></a>Anotação EnableDiscoveryClient

Adicione a anotação a seguir ao código-fonte do aplicativo.
```java
@EnableDiscoveryClient
```
Por exemplo, confira o aplicativo piggymetrics de exemplos anteriores:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="service-registry-dependency"></a>Dependência do Registro de Serviço

Para usar o serviço gerenciado Registro de Serviço do Azure, inclua a dependência `spring-cloud-starter-netflix-eureka-client` no arquivo pom.xml, conforme mostrado aqui:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

O ponto de extremidade do servidor do Registro de Serviço é injetado automaticamente como variáveis de ambiente com o aplicativo. Os aplicativos podem se registrar por conta própria no servidor do Registro de Serviço e descobrir outros microsserviços dependentes.

### <a name="distributed-configuration-dependency"></a>Dependência da Configuração Distribuída

Para habilitar a Configuração Distribuída, inclua a seguinte dependência `spring-cloud-config-client` na seção de dependências do arquivo pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Não especifique `spring.cloud.config.enabled=false` na configuração de inicialização. Caso contrário, o aplicativo deixará de funcionar com o Servidor de Configuração.

### <a name="metrics-dependency"></a>Dependência de Métricas

Inclua a dependência `spring-boot-starter-actuator` na seção de dependências do arquivo pom.xml, conforme mostrado aqui:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 As métricas são extraídas periodicamente dos pontos de extremidade JMX. Visualize as métricas usando o portal do Azure.

### <a name="distributed-tracing-dependency"></a>Dependência de Rastreamento Distribuído

Inclua as seguintes dependências `spring-cloud-starter-sleuth` e `spring-cloud-starter-zipkin` na seção de dependências do arquivo pom.xml:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

 Você também precisa habilitar uma instância do Azure Application Insights para trabalhar com sua instância de serviço do Azure Spring Cloud. Leia o [tutorial sobre rastreamento distribuído](spring-cloud-tutorial-distributed-tracing.md) para saber como usar o Application Insights com o Azure Spring Cloud.

## <a name="see-also"></a>Confira também
* [Analisar as métricas e os logs do aplicativo](https://docs.microsoft.com/azure/spring-cloud/diagnostic-services)
* [Configurar seu Servidor de Configuração](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server)
* [Usar o rastreamento distribuído com o Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-distributed-tracing)
* [Guia de Início Rápido do Spring](https://spring.io/quickstart)
* [Documentação do Spring Boot](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Próximas etapas

Neste tópico, você aprendeu a configurar seu aplicativo Java Spring para implantação no Azure Spring Cloud. Para saber como configurar uma instância de servidor de configuração, consulte o artigo a seguir.

> [!div class="nextstepaction"]
> [Saiba como configurar uma instância do Servidor de Configuração](spring-cloud-tutorial-config-server.md)

Mais exemplos estão disponíveis no GitHub: [Exemplos do Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
