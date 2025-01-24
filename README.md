# Como Fazer o Deploy de uma API na Nuvem na Prática

Este guia passo a passo mostra como realizar o **deploy de uma API** em uma **plataforma de nuvem**. Para exemplificação, vamos usar o **AWS** (Amazon Web Services), mas o processo é similar em outras plataformas como **Azure** ou **Google Cloud**. A API pode ser criada com qualquer framework de sua escolha (Node.js, Python, Java, etc.), mas para este exemplo, vamos usar uma API simples em **Node.js**.

## Pré-requisitos

Antes de começar, certifique-se de que você tem:

- Uma conta na **AWS** (ou outra plataforma de nuvem)
- O **AWS CLI** instalado e configurado
- **Node.js** e **npm** instalados
- **Git** instalado
- Familiaridade básica com a criação de APIs RESTful

## Passos para o Deploy

### Passo 1: Crie a API Localmente

Primeiro, vamos criar uma API simples em **Node.js** usando o **Express**.

Crie uma nova pasta para o seu projeto e acesse-a via terminal:
   ```bash
   mkdir minha-api
   cd minha-api
Inicialize o projeto Node.js:
   npm init -y
Instale o Express:

npm install express

Crie um arquivo chamado app.js com o seguinte código para uma API simples:

const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('API na Nuvem funcionando!');
});

app.listen(port, () => {
  console.log(`API rodando na porta ${port}`);
});

Execute a API localmente:

    node app.js

    Teste a API acessando http://localhost:3000 no seu navegador ou com o Postman.

Passo 2: Prepare a API para o Deploy

    Crie um arquivo package.json para scripts adicionais. Adicione um script de início:

{
  "name": "minha-api",
  "version": "1.0.0",
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "dependencies": {
    "express": "^4.17.1"
  }
}

Crie um arquivo .gitignore e adicione o diretório node_modules:

    node_modules

Passo 3: Configure a AWS (ou outra plataforma de nuvem)

    Se você ainda não tem o AWS CLI configurado, faça isso:

    aws configure

    Isso pedirá a chave de acesso e a chave secreta que você pode gerar no IAM da AWS, além da região onde você deseja fazer o deploy (ex: us-east-1).

Passo 4: Suba a API para um Repositório Git

    Inicie um repositório Git na sua pasta do projeto:

git init

Adicione todos os arquivos ao repositório:

git add .
git commit -m "Primeiro commit"

Crie um repositório remoto no GitHub ou em outro serviço e envie o código para lá:

    git remote add origin <URL-do-repositorio>
    git push -u origin master

Passo 5: Configure o Servidor na AWS (EC2)

Agora, vamos fazer o deploy da API na AWS usando um servidor EC2.

    Crie uma instância EC2 na AWS:
        Vá até o Console da AWS EC2.
        Crie uma nova instância, selecione uma AMI (imagem) como Amazon Linux 2 ou Ubuntu.
        Escolha um tipo de instância (ex: t2.micro para uso gratuito).
        Configure a segurança, liberando as portas 22 (SSH) e 80 (HTTP).

    Acesse sua instância EC2 via SSH:

ssh -i <sua-chave.pem> ec2-user@<endereco-publico-da-instancia>

Instale o Node.js e Git no servidor EC2:

sudo yum update -y
sudo yum install -y nodejs npm git

Clone o repositório da API:

git clone <URL-do-repositorio>
cd minha-api

Instale as dependências da API:

npm install

Inicie a API:

    npm start

Agora a API está rodando na sua instância EC2. Acesse o endereço público da instância no navegador para testar a API.
Passo 6: Configure o Nginx para Servir a API

Para tornar sua API acessível publicamente com um domínio e garantir a alta disponibilidade, você pode configurar um servidor web como Nginx.

    Instale o Nginx:

sudo yum install -y nginx

Configure o Nginx para redirecionar as requisições para sua aplicação Node.js. Edite o arquivo de configuração:

sudo vi /etc/nginx/nginx.conf

Adicione um bloco de configuração como este:

server {
    listen 80;
    server_name <seu-dominio-ou-ip-publico>;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

Reinicie o Nginx:

    sudo service nginx restart

Agora sua API está acessível através do endereço público da instância EC2, com o Nginx funcionando como proxy reverso.
Passo 7: (Opcional) Configurar um Domínio com Route 53

    Se você quiser configurar um domínio, use o serviço Route 53 da AWS para criar um domínio e configurar o apontamento para o IP da instância EC2.

    No console do Route 53, crie uma zona de hospedagem para seu domínio e adicione um registro A apontando para o IP público da sua instância EC2.

Passo 8: Monitoramento e Manutenção

Agora que a API está em produção, é importante configurar monitoramento e alertas. Você pode usar o CloudWatch da AWS para monitorar o uso de recursos, logs e a saúde da aplicação.
Conclusão

Neste guia, você aprendeu como criar uma API simples em Node.js, configurar e fazer o deploy dela em um servidor EC2 da AWS, e configurar um servidor web como Nginx para gerenciar as requisições. Esse processo pode ser adaptado a outras plataformas de nuvem, como Azure ou Google Cloud.
