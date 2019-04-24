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

Deve exibir um trecho semelhante ao abaixo :

```
CONTAINER ID        IMAGE                          COMMAND                  CREATED              STATUS              PORTS                               NAMES
a3d1dd0493f9        jupyter/datascience-notebook   "tini -g -- start-no…"   About a minute ago   Up About a minute   0.0.0.0:8891->8888/tcp              jupyter_notebook
1f284a06370c        mysql:5.7                      "docker-entrypoint.s…"   About a minute ago   Up 46 seconds       33060/tcp, 0.0.0.0:3307->3306/tcp   jupytermysqldocker_db_1

```

5. Se tudo ocorrer bem, o banco já estará criado e o docker já terá instalado o jupyter na sua máquina

### Criação das tabelas no banco ###

Para que as tabelas sejam devidamente criadas é necessário possuir o **MYSQL WORKBENCH** em sua máquina, o mesmo pode ser baixado através do link https://dev.mysql.com/downloads/workbench/

Ao executa-lo junto com os containers do docker, o mesmo irá mostrar o banco de dados criado **db** já na página inicial

Selecionar o banco e digitar o nome de usuario = root e senha = root

Selecionar o banco **PROJREDES** através do comando ```USE PROJREDES;``` e executar o script PROJREDES.sql contido nesse projeto

Pronto, as tabelas e os registros foram criados.

### Importação do projeto para o Jupyter ###
Após a criação dos containers do Jupyter e do Mysql, no diretório escolhido para hospedar o projeto, o mesmo terá criado duas pastas, a pasta modules e a pasta dataset.

**Obs.:** No linux é neceśsário alterar a permissão do usuário para essas duas pastas, pois por padrão elas vem somente com permissão de root.

1. Acessar a pasta selecionada para hospedar o projeto
2. Clonar o projeto com os códigos fontes contido no link [https://github.com/juniorjrc/jupyter-project.git]
3. Pronto, o projeto está pronto para ser rodado.

### Acessando o projeto no Jupyter Notebooks ###

Após toda a configuração acima, acessar o navegador e inserir a seguinte url http://127.0.0.1:8891/?token=dd5aa295780305544ab8183d2c510478592122cde4a3cd4a

O projeto será aberto.

Dentro da pasta work/jupyter-project/project encontra-se o documento do projeto com o nome **project.ipynb**.

Quando o projeto rodar pela primeira vez, poderá ocorrer erros de conexão com o mysql.

Para tal, basta acessar a célula que esta com erro e inserir o código de instalação da dependência

```!pip install [nome_do_modulo_com_erro] ```

Ex.:

```
!pip install pymysql

from Controller import OcorrenciaController as oc
ocorrenciaController = oc.OcorrenciaController()
ocorrenciaController.consultaSomatorioTodasOcorrenciasPeriodo(1, 1)
```
**IMPORTANTE**: Após rodar este comando, remover imediatamente o comando do pip install, pois o mesmo já instalou a dependência, caso rode a célula sem apagar o comando, o mesmo irá instalar inúmeras vezes o módulo correspondente.

Será necessário também, no arquivo contido na pasta work/jupyter-project/project/Connection/connect.py, alterar o IP, onde deve ser inserido o IP da interface do docker, para tal, verificar com o comando ipconfig (windows) ou ifconfig (linux)

Ex.:

```
import pymysql

#INSTÂNCIA DA CONEXÃO COM O BANCO
class Conexao():
    def __init__(self):
        self.conexao = pymysql.connect(
            host='{IP_DO_DOCKER_AQUI}',
            port=3307,
            database='PROJREDES',
            user='root',
            password='root'
        )

        self.bd = self.conexao.cursor()
```





