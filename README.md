# HTTPS com certificado digital ec2 docker

> Repositorio com as principais informações para a implantação do certificado digital SSL utilizando Lets Encrypt e Certbot, e configuração do Nginx que realizará o proxu reverso dentro do container docker e usando docker-compose na orquestração dos containers na instancia EC2 na aws. 

Bom, para este passo a passo, parto do principio que você ja tem uma instancia EC2 executada no console AWS, e que ela ja está com um dominio apontado para ela. Se caso não, [este repositorio](https://github.com/eliascastrosousa/script-instancia-ec2-docker) e [este](https://github.com/eliascastrosousa/registrar-dominio-instancia-aws/) pode te ajudar. Também utilizarei um projeto web pronto para ser executado na maquina, o [API REST SGB](https://github.com/eliascastrosousa/SistemadeGerenciamentodeBiblioteca-java), um laboratorio de desenvolvimento backend de APIs REST de um sistema de biblioteca realizado por mim. 

com isso em mãos, vamos lá!




