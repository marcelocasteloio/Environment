# Introdução

Esse projeto tem como objetivo mostrar um passo a passo para criarmos um ambiente de desenvolvimento baseado em [WSL (Windows Subsystem for Linux)](https://learn.microsoft.com/pt-br/windows/wsl/about) com uma distribuição [Ubuntu](https://ubuntu.com/wsl) e [docker](hhttps://www.docker.com/).

Esse ambiente será utilizado em todos os nossos vídeos, treinamentos e postagens, por isso, é importante que você tenha esse ambiente ou algo equivalente.

Se você utiliza um sistema operacional Linux ou MAC OS, você conseguirá utilizar o conteúdo desse projeto também naquilo que se diz respeito ao docker.

# Conteúdo

- [Introdução](#introdução)
- [Conteúdo](#conteúdo)
- [Visão geral](#visão-geral)
- [Instalando e Configurando WSL](#instalando-e-configurando-wsl)
- [Instalação e configuração do ambiente no Docker](#instalação-e-configuração-do-ambiente-no-docker)
- [Iniciando o ambiente após configuração do ambiente](#iniciando-o-ambiente-após-configuração-do-ambiente)
- [Parando o ambiente](#parando-o-ambiente)


# Visão geral

Todo ambiente é com base no docker, porém, é muito comum desenvolvedores .NET com C# utilizarem ambiente Microsoft Windows para o desenvolvimento e, a maioria dos iniciantes, também utilizam o sistema operacional Windows.

Embora minha preferência pessoal seja Linux, jogos e algumas ferramentas funcionam somente em ambiente Windows e ter um dual boot não é tão cômodo para as pessoas.

Visando isso, para um ambiente de desenvolvimento local, unir o Windows com o Linux por meio do WSL2 é uma excelente alternativa para aqueles que possuem um hardware com 8GB de memória RAM ou mais.

Para aqueles que utilizam diretamente alguma distribuição Linux ou MAC OS, poderão utilizar esse projeto com algumas poucas adaptações.

Por isso, esse projeto irá utilizar como cenário um computador que tenha o sistema operacional Windows, o WSL instalado e o docker instalado dentro do WSL ao invés de instalar o docker desktop para Windows.

Além disso, para evitar que seja necessário instalar alguma ferramenta para consumir as aplicações criadas (PostgreSQL, MongoDB, Redis, Kafka, RabbitMQ, Zipkin, Vault, Consul, Seq, Prometheus, Grafana e Portainer), todas possuem um client web, ou seja, é possível acessá-las pelo navegador sem a necessidade de instalar nenhuma ferramenta completar.

As ferramentas instaladas no Windows como Azure Data Studio, Robo3T e Another Redis Desktop Manager são opcionais pois temos a alterantiva do web client caso fique pesado instalar todas essas ferramentas.

A seguir, temos uma imagem geral desse ambiente local de desenvolvimento. Esse ambiente possuí as seguintes características:

- Docker
  - Todos os containers isolados dentro de uma network chamada **marcelocasteloio**.
  - Todos os containers que precisam persistir informações, como banco de dados, possuem mapeamento de volume
  -  Todas as aplicações possuem um client web para que não seja necessária a instalação de ferramentas complementares para o acesso às aplicações
  -  Containers:
     -  Banco de Dados:
        -  **PostgreSQL:** Banco de dados relacional
        -  **MongoDB:** Banco de dados NoSQL orientado à documentos
        -  **Redis:** Banco de dados NoSQL chave/valor
        -  **Consul:** Banco de dados NoSQL chave/valor (já possui web client integrado)
        -  **Vault:** Cofre de segredos (já possui web client integrado)
     -  Mensageria:
        -  **RabbitMQ:** Message broker (já possui web client integrado)
     -  Streaming:
        -  **Kafka + Zookeeper:** Processamento de streaming
     -  Observabilidade:
        -  **Seq:** Plataforma de log distribuido (já possui web client integrado)
        -  **Zipkin:** Plataforma de trace (já possui web client integrado)
        -  **Prometheus:** Time series database especializado para métricas, monitoramento e alertas (já possui web client integrado)
        -  **Open Telemetry Collector:** Coletor de log, trace e métricas na especificação do [Open Telemetry](https://opentelemetry.io/)
        -  **Grafana:** Monitoramento e alertas (já possui web client integrado)
     -  Gerenciamento de containers:
        -  **Portainer:** Gerenciador de containers (já possui web client integrado)
     -  Web Clients:
        -  **PgAdmin:** Web Client para acesso e gerenciamento do PostgreSQL
        -  **Mongo Express:** Web Client para acesso e gerenciamento do MongoDB
        -  **Redis Insight:** Web Client para acesso e gerenciamento do Redis
        -  **Kafka Manager:** Web Client para acesso e gerenciamento do Kafka
-  WSL / Ubuntu
   -  Contém o .net instalado para testes com a execução diretamente em ambiente Linux
-  Windows
   -  **WSL2:** Windows Subsystem for Linux limitado a 4GB de RAM com serviço docker instalado e configurado, mapeamento de portas entre o WSL e o Windows, bem como, compatilhamento de pastas por meio de volumes.
   -  IDEs e Editores
        -  **Visual Studio:** IDE (Integrated Development Environment ou Ambiente de Desenvolvimento Integrado) gratuita e oficial para desenvolvimento .NET (existem alternativas excelentes como o Rider da Jetbrains, porém, é paga)
        -  **Visual Studio Code:**: Editor de texto com diversos plug-ins que suprem as demandas básicas de desenvolvimento .NET
        -  **Azure Data Studio:**: Ferramenta para conexão com fontes de dados como Microsoft SQL Server e PostgreSQL por exemplo (caso não queira utilizar o web client)
        -  **Robo3T:**: Ferramenta para acesso ao MongoDB (caso não queira utilizar o web client)
        -  **Another Redis Desktop Manager:** Ferramenta para acesso ao Redis (caso não queira utilizar o web client)
        -  **Postman:** Plataforma de API para desenvolvedores. Possui testes básicos de API, suporet a comunicação HTTP, gRPC, SOAP entre outros
        -  **.NET SDK:** SDK do .NET para desenvolvimento com .NET

</br>

![Visão geral](diagrams/overview.png)


# Instalando e Configurando WSL

Esse documento não irá abordar aspectos da instalação do WSL. Caso você não tenha o WSL instalado, pode seguir a [documentação](https://learn.microsoft.com/pt-br/windows/wsl/install) oficial da Microsoft para isso. 

Para esse documento, também vou assumir que você já tenha uma distribuição Linux instalada. Caso não tenha a distribuição instalada e configurada, você pode seguir a documentação [aqui](https://ubuntu.com/tutorials/install-ubuntu-on-wsl2-on-windows-11-with-gui-support#1-overview).

A partir daqui, vou considerar que você já tenha o WSL instalado e uma distribuição linux também instalda. Daqui para frente, vou considerar que você tenha instalado a distribuição do Ubuntu.

A patir de agora, de forma opcional, vamos realizar uma simples configuração para limitar o uso de memória RAM utilizada pelo WSL. Você pode ver, com detalhes, sobre as possíveis configurações do WSL [aqui](https://learn.microsoft.com/pt-br/windows/wsl/wsl-config).

No projeto, já existe um arquivo `.wslconfig` no camnho `wsl/.wslconfig` que você pode utilizar no seu ambiente. Para criar seu próprio arquivo de configuração, siga as etapas a seguir:

1. Abra o PowerShell.
2. Navegue até o diretório do usuário:
   ```pwsh
   cd $HOME
   ```
3. Crie um arquivo chamado `.wslconfig`:
   ```pwsh
   New-Item .wslconfig
   ```
4. Abra o arquivo `.wslconfig` em um editor de texto (no exemplo abaixo, estou usando o bloco de notas):
   ```pwsh
   notepad .\.wslconfig
   ```
5. Adicione o conteúdo abaixo, salve o arquivo e pode fechar o bloco de notas:

   ```
   [wsl2]

   memory=4GB
   ```
6. Encerre o WSL:
   ```pwsh
   wsl --shutdown
   ```
7. Inicie o WSL novamente para aplicar as novas configurações:
   ```pwsh
   wsl
   ```

# Instalação e configuração do ambiente no Docker

Esse procedimento deve ser feito somente uma única vez para o setup do projeto.

A instalação do docker diretamente no WSL pode ser feita com base nessa [documentação](https://docs.docker.com/engine/install/ubuntu/). A instalação pode variar de acordo com a distribuição. O nosso foco será na configuração dos serviços e do ambiente.

A partir desse momento, será assumido que você já tenha o WSL Instalado e configurado, uma distribuicao linux funcional com o docker instalado e funcionando.

Esse documento assumirá que você clonou esse repositório no seu Windows por ser um cenário mais simples onde a maioria dos iniciantes fará, porém, a sugestão é que você clone esse repositório dentro do WSL para evitar problemas com os mapeamentos, tamanho de path e executar todo o processo dentro do seu WSL.

Para subir os serviços (containers) docker dentro do wsl, siga os passos a seguir:

1. Em um terminal, nave até a pasta raiz desse projeto. Por exemplo: C:\dev\github\marcelocasteloio\Environment :
   ```pwsh
   # Diretório de exemplo. Mude para o seu diretório 
   cd C:\dev\github\marcelocasteloio\Environment
   ```
2. Navegue até a pasta `scripts\docker`:
   ```pwsh
   cd .\scripts\docker\
   ```
3. Entre na sua instância do WSL:
   ```pwsh
   wsl
   ```
4. Configure o ambiente por meio do docker compose:
   ```pwsh
   docker compose up -d
   ```

OBS: No momento em que esse documento foi escrito, no Windows 11, foi incluido um comportamento que, até o momento, não pode ser desabilitado que é uma configuração chamada `vmIdleTimeout` que tem como valor padrão `60000 milisegundos`, ou seja, 60 segundos. Isso quer dizer que se não estiver nenhuma sessão de terminal conectada ao WSL por mais de 60 segundos, o mesmo será encerrado. Por isso, é necessário deixar uma sessão do terminal conectado ao WSL até que esse comportamento não seja mais obrigatório.

# Iniciando o ambiente após configuração do ambiente

Após toda subir o ambiente de acordo com essa [seção](#instalação-e-configuração-do-ambiente-no-docker), para iniciar o ambiente, você deve executar as seguintes etapas:

1. Em um terminal, nave até a pasta raiz desse projeto. Por exemplo: C:\dev\github\marcelocasteloio\Environment :
   ```pwsh
   # Diretório de exemplo. Mude para o seu diretório 
   cd C:\dev\github\marcelocasteloio\Environment
   ```
2. Navegue até a pasta `scripts\docker`:
   ```pwsh
   cd .\scripts\docker\
   ```
3. Entre na sua instância do WSL:
   ```pwsh
   wsl
   ```
4. Inicie o ambiente por meio do docker compose:
   ```pwsh
   docker compose start
   ```

# Parando o ambiente

Para parar os serviços que estão em execução

1. Em um terminal, nave até a pasta raiz desse projeto. Por exemplo: C:\dev\github\marcelocasteloio\Environment :
   ```pwsh
   # Diretório de exemplo. Mude para o seu diretório 
   cd C:\dev\github\marcelocasteloio\Environment
   ```
2. Navegue até a pasta `scripts\docker`:
   ```pwsh
   cd .\scripts\docker\
   ```
3. Entre na sua instância do WSL:
   ```pwsh
   wsl
   ```
4. Pare o ambiente por meio do docker compose:
   ```pwsh
   docker compose stop
   ```
