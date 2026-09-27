# 🔐 HTTPS com Certificado Digital em AWS EC2, Docker e Nginx

Este repositório tem como objetivo demonstrar, de forma prática, como configurar **HTTPS com certificado digital da Let's Encrypt**, utilizando **Certbot, Nginx e Docker Compose**, em uma aplicação backend executada dentro de containers em uma instância **AWS EC2**.

O projeto foi desenvolvido como um laboratório prático de **Cloud, Docker, Nginx, HTTPS e deploy de aplicações backend**.

---

## 📋 Sobre o projeto

O cenário utilizado neste laboratório é composto por:

```text
                    INTERNET
                        │
                        │ HTTPS
                        ▼
                 ┌─────────────┐
                 │   AWS EC2   │
                 │             │
                 │   Nginx     │
                 │      │      │
                 │      ▼      │
                 │  Backend    │
                 │  Docker     │
                 │             │
                 │  Certbot    │
                 └─────────────┘
```

A aplicação backend utilizada como exemplo é a:

**[API REST SGB — Sistema de Gerenciamento de Biblioteca](https://github.com/eliascastrosousa/SistemadeGerenciamentodeBiblioteca-java)**

Trata-se de uma API REST desenvolvida com **Java e Spring Boot**, utilizada neste laboratório para demonstrar a publicação de uma aplicação real em uma instância EC2.

---

# 🧰 Tecnologias utilizadas

- ☁️ AWS EC2
- 🐳 Docker
- 🐳 Docker Compose
- 🌐 Nginx
- 🔐 Let's Encrypt
- 🔑 Certbot
- 🐧 Linux
- 🔒 HTTPS / SSL / TLS
- ☕ Java
- 🌱 Spring Boot

---

# ⚠️ Pré-requisitos

Antes de começar, é necessário possuir:

- Uma conta na AWS;
- Uma instância EC2 criada e em execução;
- Acesso SSH à instância;
- Docker instalado na EC2;
- Docker Compose disponível;
- Um domínio registrado;
- O domínio apontando para o IP público da EC2.

Caso ainda não tenha configurado a instância ou o domínio, estes outros projetos podem ajudar:

- **[Script para configuração da instância EC2 com Docker](https://github.com/eliascastrosousa/script-instancia-ec2-docker)**
- **[Registro de domínio apontado para uma instância AWS](https://github.com/eliascastrosousa/registrar-dominio-instancia-aws)**

---

# 🚀 1. Configurando a máquina

Primeiro, conecte-se à instância EC2 através de SSH.

```bash
ssh -i chave-sgb.pem USUARIO@IP_DA_MAQUINA
```

Após conectar à máquina, clone o projeto backend que será utilizado neste laboratório:

```bash
git clone https://github.com/eliascastrosousa/SistemadeGerenciamentodeBiblioteca-java.git
```

Entre no diretório do projeto:

```bash
cd SistemadeGerenciamentodeBiblioteca-java/sgb/sgb
```

Execute a aplicação utilizando Docker Compose:

```bash
docker compose up --build
```

Após a inicialização, a API deverá estar disponível através do domínio configurado.

O Swagger pode ser acessado em:

```text
http://seu-dominio/swagger-ui/index.html
```

---

# ⚠️ 2. O problema: conexão não segura

Neste momento, a aplicação está funcionando, porém está utilizando **HTTP**.

Ao acessar o domínio pelo navegador, será exibido um aviso indicando que a conexão não é segura.

![Não seguro](https://github.com/user-attachments/assets/1a50f23b-9000-4b29-b412-2f9467003631)

Isso acontece porque ainda não existe um certificado digital configurado para o domínio.

O objetivo deste laboratório é transformar:

```text
http://seu-dominio
```

em:

```text
https://seu-dominio
```

---

# 🔐 3. Configurando o HTTPS

## O que é o Certbot?

O **Certbot** é uma ferramenta gratuita e de código aberto utilizada para automatizar a obtenção e renovação de certificados TLS da **Let's Encrypt**.

Esses certificados permitem habilitar conexões seguras através do protocolo HTTPS.

Neste projeto, o Certbot será utilizado juntamente com o Nginx e Docker para obter o certificado do domínio.

---

# 🌐 4. Configuração do Nginx + Certbot

Para realizar a configuração, será utilizado um ambiente Docker contendo:

```text
Nginx
   │
   ├── Reverse Proxy
   │
   └── HTTPS

Certbot
   │
   └── Let's Encrypt
```

O Nginx será responsável por receber as requisições HTTP/HTTPS e encaminhá-las para o backend.

O Certbot será responsável pela obtenção do certificado.

---

# 📝 5. Configurando o domínio e o e-mail

No arquivo:

```text
docker-compose-cert.yml
```

substitua o domínio e o e-mail utilizados no exemplo pelos seus próprios dados.

### Exemplo

Antes:

```text
DOMINIO_EXEMPLO
EMAIL_EXEMPLO
```

Depois:

```text
seu-dominio.com
seu-email@email.com
```

![Configuração do domínio](https://github.com/user-attachments/assets/3e70809c-47be-4e31-aeac-3fd188956bee)

Após a alteração:

![Domínio configurado](https://github.com/user-attachments/assets/dac8e7b0-b52b-4f7d-92d6-c5a147ba92bf)

> ⚠️ Utilize um e-mail válido, pois ele será utilizado pela Let's Encrypt para informações relacionadas ao certificado.

---

# 🐳 6. Configurando a versão do Nginx

No arquivo:

```text
docker/nginx.Dockerfile
```

configure a versão desejada do Nginx.

Neste laboratório foi utilizada:

```text
nginx:1.26.2-alpine
```

![Versão do Nginx](https://github.com/user-attachments/assets/f109bf91-bef9-4a4b-9493-7c644b71de0b)

O uso da variante `alpine` ajuda a manter uma imagem Docker mais enxuta.

---

# ⚙️ 7. Configurando o nginx.conf

No arquivo:

```text
config/nginx.conf
```

substitua o domínio utilizado no exemplo pelo seu domínio.

![Configuração do nginx.conf](https://github.com/user-attachments/assets/dbb40e5c-9e93-4f77-9c94-6df8d70f341e)

O Nginx será responsável por receber as requisições externas e encaminhá-las para o backend.

A arquitetura ficará aproximadamente assim:

```text
Cliente
   │
   │ HTTPS :443
   ▼
 Nginx
   │
   │ proxy
   ▼
Backend
   │
   │ :8080
   ▼
Spring Boot
```

---

# 🔌 8. Configurando o Docker Compose da aplicação

Agora é necessário alterar o arquivo:

```text
docker-compose.yml
```

da aplicação backend.

O serviço do Nginx deverá possuir a configuração necessária para:

### Porta HTTPS

Adicionar:

```yaml
ports:
  - "443:443"
```

### Certificados Let's Encrypt

Adicionar os volumes necessários para compartilhar os certificados:

```yaml
volumes:
  - /etc/letsencrypt:/etc/letsencrypt
  - /tmp/acme_challenge:/tmp/acme_challenge
```

Também devem ser ajustadas as demais configurações do serviço Nginx conforme a estrutura utilizada neste laboratório.

A configuração final ficará semelhante à apresentada abaixo:

![Configuração do Docker Compose](https://github.com/user-attachments/assets/10716509-8a68-4fa4-916f-bfdada2d3ba9)

---

# 🔑 9. Configurando o Certbot

Para realizar a instalação e configuração do Certbot, utilizei como referência a documentação oficial:

**[Certbot — Instructions](https://certbot.eff.org/instructions?ws=nginx&os=pip)**

Depois de realizar a configuração necessária, execute o Certbot para solicitar o certificado.

O processo utiliza o desafio **HTTP-01**, no qual a Let's Encrypt verifica se o domínio realmente aponta para o servidor.

O fluxo funciona aproximadamente assim:

```text
Let's Encrypt
      │
      │ solicita validação
      ▼
    Nginx
      │
      │ /.well-known/acme-challenge/
      ▼
   Certbot
      │
      │ validação
      ▼
Certificado HTTPS
```

---

# 🔒 10. Obtendo o certificado

Com o Nginx e o Certbot configurados, execute o comando correspondente à configuração do projeto.

O Certbot irá:

1. Entrar em contato com a Let's Encrypt;
2. Solicitar um certificado para o domínio;
3. Realizar a validação;
4. Receber o certificado;
5. Armazenar os arquivos do certificado;
6. Disponibilizar os arquivos para o Nginx.

Os certificados normalmente ficam em:

```text
/etc/letsencrypt/
```

---

# 🌐 11. Acessando a aplicação com HTTPS

Após a emissão e configuração do certificado, a aplicação deverá estar disponível através de:

```text
https://seu-dominio/swagger-ui/index.html
```

Agora o navegador deverá reconhecer a conexão como segura:

```text
🔒 https://seu-dominio
```

Em vez de:

```text
⚠️ http://seu-dominio
```

---

# 🏗️ Arquitetura final

Ao final da configuração, teremos:

```text
                         INTERNET
                            │
                            │ HTTPS :443
                            ▼
                  ┌──────────────────┐
                  │      AWS EC2     │
                  │                  │
                  │  ┌────────────┐  │
                  │  │   Nginx    │  │
                  │  │            │  │
                  │  │ TLS/HTTPS  │  │
                  │  │ Reverse    │  │
                  │  │ Proxy      │  │
                  │  └─────┬──────┘  │
                  │        │         │
                  │        ▼         │
                  │  ┌────────────┐  │
                  │  │  Backend   │  │
                  │  │ Spring Boot│  │
                  │  └────────────┘  │
                  │                  │
                  │  ┌────────────┐  │
                  │  │  Certbot   │  │
                  │  │ Let's      │  │
                  │  │ Encrypt    │  │
                  │  └────────────┘  │
                  │                  │
                  └──────────────────┘
```

---

# 🔄 Fluxo da requisição

Depois da configuração, uma requisição feita pelo usuário seguirá este caminho:

```text
https://seu-dominio.com
          │
          ▼
        DNS
          │
          ▼
      AWS EC2
          │
          ▼
       Nginx
          │
          │ HTTPS → HTTP interno
          ▼
    Spring Boot
          │
          ▼
       API REST
```

O certificado TLS fica concentrado no Nginx, que atua como **Reverse Proxy**.

---

# 🔐 Por que utilizar Nginx?

O Nginx permite centralizar algumas responsabilidades que não precisam ficar diretamente dentro da aplicação Spring Boot.

Neste cenário, ele atua como:

- Reverse Proxy;
- Terminador TLS;
- Servidor HTTPS;
- Controlador das portas externas;
- Intermediário entre Internet e aplicação.

Assim, a aplicação Spring Boot pode continuar executando internamente no container, enquanto o Nginx cuida da exposição HTTP/HTTPS.

---

# 🐳 Por que utilizar Docker?

O uso do Docker permite separar os componentes da infraestrutura em containers.

Neste projeto temos, conceitualmente:

```text
┌─────────────────────────────┐
│          Docker             │
│                             │
│  ┌──────────┐ ┌──────────┐  │
│  │ Backend  │ │  Nginx   │  │
│  └──────────┘ └──────────┘  │
│                             │
│       ┌──────────┐          │
│       │ Certbot  │          │
│       └──────────┘          │
└─────────────────────────────┘
```

Isso facilita a configuração, reprodução e manutenção do ambiente.

---

# 🧠 Principais conceitos praticados

Este laboratório permite praticar conceitos importantes de:

### ☁️ Cloud

- AWS EC2;
- DNS;
- IP público;
- configuração de servidor;
- acesso SSH.

### 🐳 Docker

- Dockerfile;
- Docker Compose;
- containers;
- volumes;
- redes;
- exposição de portas.

### 🌐 Web

- HTTP;
- HTTPS;
- TLS/SSL;
- portas 80 e 443;
- Reverse Proxy.

### 🔐 Segurança

- Certificados digitais;
- Let's Encrypt;
- Certbot;
- HTTPS;
- validação de domínio.

### ⚙️ Infraestrutura

- Linux;
- Nginx;
- deploy;
- configuração de servidor;
- integração entre containers.

---

# 📚 O que aprendi com este projeto

Este projeto foi desenvolvido como um laboratório prático para compreender o caminho completo entre uma aplicação backend e sua publicação na Internet.

Entre os principais aprendizados estão:

- Criar e configurar uma instância EC2;
- Publicar uma aplicação Docker na AWS;
- Configurar DNS para uma aplicação;
- Utilizar Nginx como Reverse Proxy;
- Configurar HTTPS;
- Obter certificados utilizando Let's Encrypt;
- Utilizar Certbot;
- Compartilhar certificados entre containers através de volumes;
- Trabalhar com Docker Compose;
- Entender a comunicação entre Nginx e uma aplicação Spring Boot;
- Identificar problemas relacionados a HTTP, HTTPS, DNS e certificados.

---

# 🔗 Projetos relacionados

### ☁️ Configuração da EC2

[Script para configuração de instância EC2 com Docker](https://github.com/eliascastrosousa/script-instancia-ec2-docker)

### 🌐 Configuração do domínio

[Registro de domínio e apontamento para AWS](https://github.com/eliascastrosousa/registrar-dominio-instancia-aws/)

### ☕ Aplicação Backend

[API REST — Sistema de Gerenciamento de Biblioteca](https://github.com/eliascastrosousa/SistemadeGerenciamentodeBiblioteca-java)

---

# 👨‍💻 Autor

**Elias Castro**

Analista e Desenvolvedor de Sistemas.

### Tecnologias relacionadas

```text
Java
Spring Boot
REST APIs
Docker
Docker Compose
AWS EC2
Nginx
Linux
HTTPS / TLS
Git / GitHub
```

---

## 📄 Licença

Este projeto está disponível para fins de **estudo, laboratório e aprendizado de infraestrutura e deploy de aplicações backend**.
