# HTTPS com certificado digital ec2 docker

> Este repositorio tem o intuito de ensinar como adquirir um certificado SSL/HTTPS para sua aplicação utilizando Lets Encrypt e Certbot, no cenario de um projeto backend rodando dentro do container e usando docker-compose na orquestração dos containers em uma instancia EC2 na aws. 

Para este passo a passo, é necessario que você ja tenha uma instancia EC2 executada no console AWS e que ela ja esteja com um dominio apontado para ela. Se caso não, este repositorio [script-instancia-ec2-docker](https://github.com/eliascastrosousa/script-instancia-ec2-docker) e este [registrar-dominio-instancia-aws](https://github.com/eliascastrosousa/registrar-dominio-instancia-aws/) pode te ajudar. Também utilizarei um projeto backend pronto para ser executado na instancia, a [API REST SGB](https://github.com/eliascastrosousa/SistemadeGerenciamentodeBiblioteca-java), um laboratorio de desenvolvimento backend de APIs REST de um sistema de biblioteca realizado por mim com Java e Spring Boot. 

com isso em mãos, vamos lá!


## Configurando a maquina

```
  sudo ssh -i chave-sgb.pem USUARIO@IP_DA_MAQUINA 
```

Clonando o repositorio do projeto que irei rodar no servidor e rodando o docker compose

```
git clone https://github.com/eliascastrosousa/SistemadeGerenciamentodeBiblioteca-java.git
```

```
 cd SistemadeGerenciamentodeBiblioteca-java/sgb/sgb
```

```
 docker compose up --build
```

Apartir deste momento, você ja deve conseguir acessar o swagger da aplicação pelo browser da sua maquina desta forma:

```
http://seu-dominio/swagger-ui/index.html
```
Porém ele ira aparecer a informação de não seguro: 
![nao seguro](https://github.com/user-attachments/assets/1a50f23b-9000-4b29-b412-2f9467003631)

Então vamos para a configuração do Certbot

### Incluindo o certificado HTTPS

> O Certbot é uma ferramenta de software gratuita e de código aberto que automatiza o processo de obtenção e renovação de certificados SSL/TLS da Let's Encrypt. Esses certificados são usados para habilitar conexões seguras (HTTPS) em sites. 

Utilizarei o repositorio do xxx para fazer a configuração do nginx e certbot dentro do docker-compose e obter o certificado. Será mais ou menos assim:

Passo 1:

Subtituição do dominio e email que estão escritos no arquivo docker-compose-cert.yml para o meu:

![image](https://github.com/user-attachments/assets/3e70809c-47be-4e31-aeac-3fd188956bee)

para este: 

![image](https://github.com/user-attachments/assets/dac8e7b0-b52b-4f7d-92d6-c5a147ba92bf)

Passo 2: 
Fazer a alteração da versão do nginx no arquivo docker/nginx.Dockerfile, no meu caso estou utilizando a versão 1.26.2-alpine

![image](https://github.com/user-attachments/assets/f109bf91-bef9-4a4b-9493-7c644b71de0b)

Passo 3: 

Subistitua o dominio para o seu dentro do arquivo config/nginx.conf

![config/nginx.conf](https://github.com/user-attachments/assets/dbb40e5c-9e93-4f77-9c94-6df8d70f341e)

Passo 3:

Adcione ou substitua no arquivo docker-compose.yml da SUA aplicação, as seguintes informações do serviço nginx:

- ports: adicione a porta 443:443
- volumes: adicione os diretorios do letsencrypt e acme_challenge
- altere demais informações como no print a seguir

ficara então desta forma: 

![image](https://github.com/user-attachments/assets/10716509-8a68-4fa4-916f-bfdada2d3ba9)








Para a instalação do Certbot, utilizei os passos da sua propria documentação: (Site Certbot)[https://certbot.eff.org/instructions?ws=nginx&os=pip], feita a instalação rode o comendo

