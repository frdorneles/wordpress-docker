## wordpress-docker

## Projeto de Bloco

Este trabalho foi desenvolvido para obter conhecimento da ferramenta GITHUB referente a instalação do Wordpress com Containers Docker.

## Docker e Contaneirs

Docker se resume em uma tecnologia semelhante a uma virtualização tradicional, que possibilita o empacotamento de uma aplicação ou ambiente inteiro dentro de um contêiner, daí temos a facilidade de portabilidade para qualquer outro ambiente com Docker instalado.

![image](https://user-images.githubusercontent.com/90536330/141665815-d9579080-b469-41c6-9fdd-f94e74f8d2f5.png)

Containers é a segregação dos processos no mesmo kernel, além de conter toda a aplicação instalada, também contém tudo o que você precisa para que seu sistema funcione (SO, bibliotecas, softwares, arquivos, etc).

![image](https://user-images.githubusercontent.com/90536330/141666436-2b73db67-c23a-4d7d-a4d0-7eec7fda524a.png)

Diferente das máquinas virtuais, o Docker dispensa uma camada com um hypervisor. Em vez disso, os containers funcionam como processos isolados, compartilhando diretamente o mesmo kernel da máquina hospedeira junto com outros containers e quaisquer outros serviços na mesma.

![image](https://user-images.githubusercontent.com/90536330/141666704-40fc3a99-9c19-4f00-ad7f-4bd19f1897ce.png)

Próximos passos será instalar o Docker, cada contêiner é uma aplicação isolada independentemente, vamos criar um contêiner para o Worpress e outro para o MySQL.

O Wordpress é um aplicativo para um sistema de gerenciamento de conteúdo voltado para a web, sua descrição é toda em PHP e os bancos de dados (para armazenamento dos conteúdos) é o MySQL.

## Instalação do Docker

Pré-requisito: aplicar o comando abaixo na pasta /etc/sudoers para aplicar permissões ao usuário.

```
ansible ALL=(ALL:ALL) NOPASSWD:ALL
```

Criar pasta com o comando mkdir.

```
mkdir wordpress-docker
```

Os comandos abaixo instalam vários pacotes de suporte, que serão necessários para as funções de funcionamento dos pacotes do Docker.

```
sudo apt-get install \
  apt-transport-https \
  ca-certificates \
  curl \
  software-properties-common
```
Com o comando curl se faz o download 

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

Este comando exibe a identificação (fingerprint) da chave que acabamos de adicionar. O valor "key fingerprint" exibido deve ser 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88.

```
sudo apt-key fingerprint 0EBFCD88
```

Vamos atualizar a lista de pacotes disponíveis em nosso repositório, isto é necessários para complementar todo o repositório Docker que acabamos de adicionar.

```
sudo apt-get update
```

Este comando adiciona o repositório de pacotes do projeto Docker ao nosso sistema. Assim, vamos poder baixar a instalar os softwares disponibilizados pelo projeto (incluindo o próprio Docker).

```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

Agora estamos instalando, finalmente, o sistema Docker. É ele quem vai permitir que executemos containers como o do banco MySQL e do WordPress.

```
sudo apt-get install docker-ce
```

## Criando Playbook para Containers Docker

Primeiro passo, dentro do diretório chamado de wordpress-docker, vamos criar o arquivo que vai conter nosso playbook, chamado de playbook.yaml, e outro arquivo chamado de hosts onde vai armazenar nosso servidor.

```
cd wordpress-docker
touch playbook.yml
touch hosts
```

A seguir vejamos nosso código de como transformar os comandos de Docker em um playbook Ansible. Para isso apenas lembre-se de ajustar o hosts e o user de acordo com seu inventário.

```
---
 - hosts: wordpress
   remote_user: ansible
   become: yes
   tasks:
     - name: "Executa o container MySQL"
       docker_container:
        name: banco
        image: mysql:5.7
         env:
           MYSQL_ROOT_PASSWORD: senha123
           MYSQL_DATABASE: wordpress
           MYSQL_USER: wordpress
           MYSQL_PASSWORD: senha123
     - name: "Executa o container WordPress"
       docker_container:
         name: wordpress
         image: wordpress
         links:
           - "banco:mysql"
```

Antes de executar o playbook temos que instalar as bibliotecas necessárias de comunicação entre o Docker, Ansible e o ESXi.

```
sudo apt-get install python-pip
```

ou:

```
sudo apt-get install python3-docker
```

Comando das bibliotecas ao ESXi.

```
sudo pip3 install docker
```

Para verificar se os containers estão em execução, aplicar o comando a seguir:

```
sudo docker ps
```

Agora vamos executar nosso playbook com o comando abaixo:

```
ansible-playbook -i hosts playbook.yml
```

Resultado do nosso playbook.

![image](https://user-images.githubusercontent.com/90536330/141667892-31f72175-ff5b-4d04-8835-8aa2faab2dba.png)

Acessando nossa aplicação.

![image](https://user-images.githubusercontent.com/90536330/141667922-463b435d-00ef-4527-924b-7550c41a5572.png)


