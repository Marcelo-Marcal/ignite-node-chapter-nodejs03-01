# ignite-node-chapter-nodejs03-01
### Chapter III

## 1 Conhecendo o Docker:
### Criando nosso primeiro container e Dockerfile
  crisção do Dockerfile
img 01

  criação do .dockerignore
img 02

Rodar: para criar uma imagem
docker build -t rentx .

Rodar: rodar o docker
docker run -p 3333:3333 rentx
img 03

Caso querira acessa o conteine criado
docker ps

docker exec -it NOME DO CONTEINE /bin/bash

img 04

E queira ver se as informações estão ok

ls

### Usando docker-compose

Criação de um arquivo chamado:
  docker-compose.yml

Instalação do docker compose
  https://www.notion.so/Docker-e-Docker-Compose-16771f2ceefe4a05a8c29df4ca49e97a


Execultar o docker compose:
docker-compose up

OBS: Se o conteine ja estive rodando vamos para ele e reover para criar um novo

docker ps
docker stop E ONUMERO DO CONTEINE
docker rm E ONUMERO DO CONTEINE

docker-compose up

Para execulta o docker e ele fique rodando mesmo que feche o terminal.
Continua rodando em Background

docker-compose up -d

### Comandos do docker

Ver todos o conteines de pé:
  docker ps

ver todos o conteine ate os que estão parados:
  docker ps -a

Remover o conteneiner, antes de fazer a remoção ele precisa ser parado:
  docker rm ONUMERO DO CONTEINE

Reinicia o conteiner:
  docker start ONUMERO DO CONTEINE

Para um conteiner:
  docker stop E ONUMERO DO CONTEINE

Para o serviço:
  docker-compose stop

Remover tudo o que tem no conteine:
  docker-compose down

Acessa uma maquina
  docker exec -it NOME DO CONTEINER /bin/bash

Ver os logs que estão passando na aplicação:
  docker logs NOME DA APLICAÇÂO

E pra observa o logs
  docker logs -f

## 2 Trabalhando com Banco de Dados

### Conhecendo as formas de usar o banco de dados

MODEL <-> ORM <-> BANCO DE DADOS

O ORM, ira pega o codigo e transforma em uma forma que o banco de dados entenda.

### Instalando o TypeORM
https://typeorm.io/#/
  
01 - Install the npm package:
yarn add typeorm

02 - You need to install reflect-metadata shim:
yarn add typeorm reflect-metadata

Pode ser ignorado o passo tres da instalação.

04 - Install a database driver: Escolher banco de dados.
  for PostgreSQL or CockroachDB

yarn add pg

Configurações no projeto no arquivo tsconfig.json:
Abilitar:
  "emitDecoratorMetadata": true,
  "experimentalDecorators": true,

### Criando container do postgres

img05

docker-compose up --force-recreate

Verificar o IP do conteiner:

docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' rentx
Ou
docker exec database_ignite cat /etc/hosts
docker exec rentx cat /etc/hosts

Arrumando erro de comunicação:
img06
docker-compose down
docker-compose up -d
docker logs rentx -f
img07

Abrir o Beekeeper-Studio: para trabalhar com banco de dados.
img08
Selecionar o tipo: Postgres
Usuario: docker
Senha: ignite
Default Database: rentx

Se der um test ele ira mostra se ja esta conectado
img09

Ai e so dar um Connect

img010

- Refatoração com network_mode e reload
Refatoração Docker com TypeORM
https://www.notion.so/Refatora-o-Docker-com-TypeORM-4500fc0d075349ac9b97d670e734d41b

### Aprendendo o conceito de migrations

Criação das tabelas e colunas no banco de dados
Poderia ser criado a mão direto no Beekeeper com comandos como por exemplo:
img011

### Criando migration de categoria

Criar um scripts no arquivo package.json:
"typeorm": "ts-node-dev ./node_modules/typeorm/cli"

yarn typeorm

E mais uma alteração dentro do ormconfig.json:
definido a pasta onde estara nosso projeto. e para isso vamos criar uma pasta dentro de database, com o nome de migrations.
E dentro do ormconfig.json:
Criar:
img012

Criação da migration:
yarn typeorm migration:create -n CreateCategories

img014

img015

yarn typeorm migration:run

img016

Pra ver se esta tudo certo e so ir ao Beekeeper

Para desfazer pra arrumar:

yarn typeorm migration:revert


### Refatorando o model de categoria

Altera o nome da pasta model para entities
img018

### Alterando o Repositório de categoria

docker-compose start

docker ps

### Refatorando o caso de uso de categoria

### Entendendo as alterações

Reiniciar os serviços que não estão de pé:
docker-compose start

## 3 Injeção de dependência

### Conhecendo TSyringe
Funciona como um facilitador de dependencia

yarn add tsyringe

Agora vamos fazer as injeções do UseCase

Criar uma pasta no src, chamada: shared
E dentro da pasta shared, criar outra pasta: container
E dentro da pasta: container criar um arquivo: index.ts

img019

### Refatorando as especificações

### Criando migration de especificação

Criação da migration:
yarn typeorm migration:create -n CreateSpecifications

Rodar a migration:
yarn typeorm migration:run

### Continuação da documentação

Vamos entra no no arquivo: swagger.json:
http://localhost:3333/api-docs/

Criação da documentação de importe e specificação
img020

Criação de aplicação para arquivo csv:
"multipart/form-data": { }

```
"multipart/form-data": {
              "schema": {
                "type": "object",
                "properties": {
                  "file": {
                    "type": "string",
                    "format": "binary"
                  }
                }
              }
            }
```
img021

## 4 Usuário

### Criando migration de usuário

Criação da migration com a tabela de Usuario:
yarn typeorm migration:create -n CreateUsers

img022

Rodar a migration:
yarn typeorm migration:run

Criar a entidade de Usuario:
E criar um modulo, na pasta modules outra pasta chamado: accounts.
E dentro da pasta chamada accounts, outra pasta chamada: entities.
E dentro da pasta entities, um arquivo chamado: User.ts

### Criando repositório de usuário

Criar a dentro de accounts uma pasta repositories
E criar a interface dentro da mesma pasta

IUsersRepository.ts

### Criando controller de usuário

### Alterar tabela de usuário

Criação da migration com a tabela de Aterar  e deletar Usuario:
yarn typeorm migration:create -n AlterUserDeleteUsername


Rodar a migration:
yarn typeorm migration:run

### Criptografrar senha
Instalar a biblioteca:
yarn add bcrypt

Instalar as tipagens:
yarn add @types/bcrypt -D

Verificação para ver se já existe um usuario com esse email.
Que ira busca dentro do usersRepository
img023

### Entendendo autenticação com JWT
Vai gerar um token

### Criando token do usuário
Instalar a biblioteca:
yarn add jsonwebtoken

Instalar as tipagens:
yarn add @types/jsonwebtoken -D

https://www.md5hashgenerator.com/

Teste no insomnia:
img024

E para trazer somente o nome e email:
img025

img026

### Autenticação nas rotas

### Tratamento de exceções

Instalar a biblioteca:
yarn add express-async-errors

Log
docker logs rentx -f

## Avatar de Usuário
### Adicionando coluna de avatar

criar uma pasta updateUserAvatar
E criar um arquivo dentro dela: UpdateUserAvatarUseCase.ts

- Adicionar coluna avatar na tabela de users
  Criação da migration com a tabela de Aterar  e deletar Usuario:
  yarn typeorm migration:create -n AlterUserAddAvatar

  Rodar a migration:
  yarn typeorm migration:run

- Refatotora usuario com coluna avatar: entities/User.ts
  @Column()
  avatar: string;

- Configuração upload no multer

- Criar a regra de negocio do upload

- Criar controller
  
Crias uma pasta dentro do src/ @type
Destro dela outra pasta @type/ express
Destro dela outra pasta express/ index.d.ts

### Upload de avatar
```Deleta manualmente no banco de dados:```
` DELETE FROM USERS WHERE ID = 'c3b63a2a-1ea0-4339-bd18-f9ca03cd7f51' `

### Remover arquivo de avatar existente

Crias uma pasta dentro do src/ utils
Destro dela outra pasta utils/ file.ts

Ateração dentro de UpdateUserAvatarUseCase.ts
img027

Fim