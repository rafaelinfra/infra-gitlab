# GitLab com Docker Compose

Este guia descreve como instalar e executar o GitLab utilizando Docker Compose, além de configurar a autenticação com Azure Active Directory.

## Pré-requisitos

Antes de iniciar, certifique-se de que possui os seguintes itens instalados:

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

## Instalação do Docker e Docker Compose

### Instalação do Docker

1. Atualize os pacotes do sistema:
   ```sh
   sudo apt update && sudo apt upgrade -y
   ```
2. Instale os pacotes necessários:
   ```sh
   sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
   ```
3. Adicione o repositório oficial do Docker:
   ```sh
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```
4. Instale o Docker:
   ```sh
   sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io
   ```
5. Verifique se o Docker está rodando:
   ```sh
   sudo systemctl status docker
   ```

### Instalação do Docker Compose

1. Baixe e instale a versão mais recente do Docker Compose:
   ```sh
   sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   ```
2. Dê permissão de execução:
   ```sh
   sudo chmod +x /usr/local/bin/docker-compose
   ```
3. Verifique a instalação:
   ```sh
   docker-compose --version
   ```

## Criando o App Registration no Azure AD

Para configurar a autenticação com Azure Active Directory, siga os passos abaixo:

1. Acesse o portal do [Azure Active Directory](https://portal.azure.com/).
2. Vá para **Azure Active Directory** > **App registrations**.
3. Clique em **New registration**.
4. Preencha os seguintes campos:
   - **Name**: Nome do aplicativo (ex: `GitLab Auth`)
   - **Supported account types**: Escolha "Accounts in this organizational directory only".
   - **Redirect URI**: Escolha "Web" e insira a URL:
     ```
     http://localhost:10443/users/auth/azure_activedirectory_v2/callback
     ```
5. Após a criação, anote os seguintes valores:
   - **Application (client) ID**
   - **Directory (tenant) ID**
   - **Client Secret** (Gerado em "Certificates & secrets")

## Subindo o GitLab com Docker Compose

1. Clone o repositório [infra-gitlab](https://github.com/rafaelinfra/infra-gitlab.git) 

   Substitua `SEU_CLIENT_ID`, `SEU_CLIENT_SECRET` e `SEU_TENANT_ID` pelos valores obtidos no Azure AD.

2. Suba os serviços do GitLab:
   ```sh
   docker-compose up -d
   ```

## Acompanhamento de Logs

Para visualizar os logs do GitLab em tempo real, utilize o comando:
   ```sh
   docker-compose logs -f
   ```

## Links de Acesso

- Acesse o GitLab através do navegador: [http://localhost:10443](http://localhost:10443)
- Para acessar via SSH:
   ```sh
   ssh -p 2224 git@localhost
   ```

## Parando e Removendo os Contêineres

Para parar os serviços:
   ```sh
   docker-compose down
   ```

Caso deseje remover os volumes associados:
   ```sh
   docker-compose down -v
   ```

