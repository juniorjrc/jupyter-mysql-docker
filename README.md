# MANUAL DE INSTALAÇÃO AUTOMÁTICA DO JUPYTER/MYSQL/DOCKER #

## Para efeitos de instalação e correto funcionamento de todos os trechos abaixo, compreende-se que você já possui em sua máquina o docker e o docker-compose devidamente instalado ##

### Definição do diretório do jupyter ###

O próximo passo é definir o diretório onde será instalado todo o projeto.

1. Definir o diretório onde ficará instalado todo o projeto (Ex.: C://SEU_USUARIO//Meus documentos ou /home/SEU_USUARIO/Documentos)
2. Deixar o arquivo **docker-compose.yml** da seguinte forma

```
version: '3.3'
services:
  db:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: 'PROJREDES'
      # So you don't have to use root, but you can if you like
      MYSQL_USER: 'root'
      # You can use whatever password you like
      MYSQL_PASSWORD: 'root'
      # Password for root access
      MYSQL_ROOT_PASSWORD: 'root'
    ports:
      # <Port exposed> : < MySQL Port running inside container>
      - '3307:3306'
    expose:
      # Opens port 3306 on the container
      - '3306'
      # Where our data will be persisted
    volumes:
      - my-db:/var/lib/mysql

  datascience-notebook:
      image:    jupyter/datascience-notebook
      volumes:
        - {CAMINHO_DO_DIRETORIO_QUE_ESCOLHEU_ONDE_FICARA_INSTALADO_O_PROJETO}:/home/jovyan/work
        - {CAMINHO_DO_DIRETORIO_QUE_ESCOLHEU_ONDE_FICARA_INSTALADO_O_PROJETO}:/home/jovyan/work/datasets
        - {CAMINHO_DO_DIRETORIO_QUE_ESCOLHEU_ONDE_FICARA_INSTALADO_O_PROJETO}:/home/jovyan/work/modules
        - {CAMINHO_DO_DIRETORIO_QUE_ESCOLHEU_ONDE_FICARA_INSTALADO_O_PROJETO}:/etc/ssl/notebook
      ports:
        - 8891:8888
      container_name:   jupyter_notebook
# Names our volume
volumes:
  my-db:
```

3. Após as alterações no arquivo, rodar o seguinte comando no terminal (Obs.: rodar o comando no diretório escolhido para hospedar o projeto)

```docker-compose up```

4. Após a instalação do container, rode o comando ```docker container ps -a``` para verificar se os container estão funcionando

5. Se tudo ocorrer bem, o banco já estará criado e o docker já terá instalado o jupyter na sua máquina

### Criação das tabelas no banco ###

Para que as tabelas sejam devidamente criadas é necessário possuir o **MYSQL WORKBENCH** em sua máquina, o mesmo pode ser baixado através do link https://dev.mysql.com/downloads/workbench/

Ao executa-lo junto com os containers do docker, o mesmo irá mostrar o banco de dados criado **db** já na página inicial

Selecionar o banco e digitar o nome de usuario = root e senha = root

Selecionar o banco **PROJREDES** através do comando ```USE PROJREDES;``` e executar o script PROJREDES.sql contido nesse projeto

Pronto, as tabelas e os registros foram criados.

### Importação do projeto para o Jupyter ###
Após a criação dos containers do Jupyter e do Mysql, no diretório escolhido para hospedar o projeto, o mesmo terá criado duas pastas, a pasta modules e a pasta dataset.

1. Acessar a pasta dataset
2. Clonar o projeto com os códigos fontes contido no link [https://github.com/juniorjrc/jupyter-project.git]
3. Pronto, o projeto está pronto para ser rodado.

### Acessando o projeto no Jupyter Notebooks ###

Após toda a configuração acima, acessar o navegador e inserir a seguinte url demonstrada no terminal para o jupyter

O projeto será aberto.

Dentro da pasta work/dataset/project encontra-se o documento do projeto.

Quando o projeto rodar pela primeira vez, poderá ocorrer erros de conexão com o mysql.

Para tal, basta acessar a célula que esta com erro e inserir o código de instalação da dependência

```!pip install [nome_do_modulo_com_erro] ```

Será necessário também, no arquivo contido na pasta work/dataset/project/Connection/connect.py, alterar o IP, onde deve ser inserido o IP da interface do docker, para tal, verificar com o comando ipconfig (windows) ou ifconfig (linux)





