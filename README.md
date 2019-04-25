# MANUAL DE INSTALAÇÃO AUTOMÁTICA DO JUPYTER/MYSQL/DOCKER #

## Para efeitos de instalação e correto funcionamento de todos os trechos abaixo, compreende-se que você já possui em sua máquina o docker e o docker-compose devidamente instalado ##

### Definição do diretório do jupyter ###

O próximo passo é definir o diretório onde será instalado todo o projeto.

1. Definir o diretório onde ficará instalado todo o projeto (Ex.: C://SEU_USUARIO//Meus documentos ou /home/SEU_USUARIO/Documentos)
2. Rodar o seguinte comando para gerar a imagem personalizada do banco
```
docker build -t db-project .
```
3. Para verificar se a imagem foi criada, executar o comando
```
docker image ls
```

Deve aparecer a imagem 'db-project'

3. Deixar o arquivo **docker-compose.yml** da seguinte forma

```
version: '3.3'
services:
  db:
    image: db-project
    environment:
      MYSQL_DATABASE: 'PROJREDES'
      # So you don't have to use root, but you can if you like
      MYSQL_USER: 'MainUser'
      # You can use whatever password you like
      MYSQL_PASSWORD: 'MainPassword'
      # Password for root access
      MYSQL_ROOT_PASSWORD: 'RootPassword'
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
        - /home/jorgejr/Documentos/Projeto Redes:/home/jovyan/work
        - /home/jorgejr/Documentos/Projeto Redes:/home/jovyan/work/datasets
        - /home/jorgejr/Documentos/Projeto Redes:/home/jovyan/work/modules
        - /home/jorgejr/Documentos/Projeto Redes:/etc/ssl/notebook
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
CONTAINER ID        IMAGE                          COMMAND                  CREATED             STATUS              PORTS                               NAMES
52676f8903db        jupyter/datascience-notebook   "tini -g -- start-no…"   10 minutes ago      Up 10 minutes       0.0.0.0:8891->8888/tcp              jupyter_notebook
1945c3497427        db-project                     "docker-entrypoint.s…"   10 minutes ago      Up 10 minutes       33060/tcp, 0.0.0.0:3307->3306/tcp   jupytermysqldocker_db_1
```

5. Se tudo ocorrer bem, o banco já estará criado com as devidads tabelas e o docker já terá instalado o jupyter na sua máquina

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

Será necessário, no arquivo contido na pasta work/jupyter-project/project/Connection/connect.py, alterar o IP, onde deve ser inserido o IP da interface do docker, para tal, verificar com o comando ipconfig (windows) ou ifconfig (linux)

Ex.:

```
try:
    import pymysql
except:
    import subprocess
    subprocess.call(["pip", "install", "pymysql"])
    import pymysql

#INSTÂNCIA DA CONEXÃO COM O BANCO
class Conexao():
    def __init__(self):
        self.conexao = pymysql.connect(
            host='172.17.0.1',
            port=3307,
            database='PROJREDES',
            user='MainUser',
            password='MainPassword'
        )

        self.bd = self.conexao.cursor()
```





