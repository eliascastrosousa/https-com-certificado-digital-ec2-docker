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

### Instalando o Certbot

> O Certbot é uma ferramenta de software gratuita e de código aberto que automatiza o processo de obtenção e renovação de certificados SSL/TLS da Let's Encrypt. Esses certificados são usados para habilitar conexões seguras (HTTPS) em sites. 

Para a instalação do Certbot, utilizei os passos da sua propria documentação: (Site Certbot)[https://certbot.eff.org/instructions?ws=nginx&os=pip], feita a instalação rode o comendo

