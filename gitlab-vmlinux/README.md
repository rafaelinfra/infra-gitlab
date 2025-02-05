# Guia de Instalação do GitLab Server no Ubuntu 22.04

## 1. Atualizar o Sistema

Antes de instalar qualquer coisa, atualize os pacotes:

```bash
sudo apt update && sudo apt upgrade -y
```

## 2. Instalar Dependências

O GitLab requer algumas dependências:

```bash
sudo apt install -y curl openssh-server ca-certificates tzdata perl
```

Se quiser configurar um servidor SMTP para envio de e-mails, instale o **Postfix**:

```bash
sudo apt install -y postfix
```

Na configuração do Postfix, selecione **"Internet Site"** e defina o nome do host.

## 3. Adicionar o Repositório Oficial do GitLab

```bash
curl -fsSL https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
```

## 4. Instalar o GitLab Server

```bash
sudo apt install -y gitlab-ce
```

## 5. Configurar URL do GitLab

Edite o arquivo de configuração:

```bash
sudo nano /etc/gitlab/gitlab.rb
```

Altere a linha:

```ruby
external_url 'http://seu-ip-ou-dominio'
```

Para:

```ruby
external_url 'http://gitlab.seudominio.com'
```

Aplique as mudanças:

```bash
sudo gitlab-ctl reconfigure
```

## 6. Configurar Rede no Azure

- No portal do Azure, acesse sua VM e vá em **Rede** → **Grupos de Segurança de Rede (NSG)**.
- Adicione regras de entrada para liberar as portas:
  - **HTTP (80)**
  - **HTTPS (443)**
  - **SSH (22)**
  - **Git (22, 443, 80, 8080, 2222)**
- Configure um **IP Público Estático** na VM.

## 7. Configurar Domínio no Azure

1. No Azure, acesse **Azure DNS Zones** e adicione um **Registro A**:
   - **Nome:** `gitlab`
   - **Tipo:** `A`
   - **Valor:** **IP Público da VM**
2. Se estiver usando outro provedor de domínio, faça essa configuração no painel de DNS dele.

## 8. Configurar HTTPS com Let's Encrypt

Edite o arquivo de configuração do GitLab:

```bash
sudo nano /etc/gitlab/gitlab.rb
```

Altere:

```ruby
external_url 'https://gitlab.seudominio.com'
letsencrypt['enable'] = true
letsencrypt['contact_emails'] = ['seu@email.com']
letsencrypt['auto_renew'] = true
```

Aplique as mudanças:

```bash
sudo gitlab-ctl reconfigure
```

## 9. Testar e Acessar

Acesse no navegador:

```
https://gitlab.seudominio.com
```

verifique os logs:

```bash
sudo gitlab-ctl tail
```

