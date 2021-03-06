# Funcional PHP Challenge

## Objetivo

Desenvolver uma API em PHP + Laravel que simule algumas funcionalidades de um banco digital.
Nesta simulação considere que não há necessidade de autenticação.

## Desafio

Você deverá garantir que o usuário conseguirá realizar uma movimentação de sua conta corrente para quitar uma dívida.

## Introdução 

Utilizando PHP + Laravel + Postgres + GraphQL, foi desenvolvido uma API pública que simula uma simples movimentação de conta bancária.

## Clonando Repositorio

Abra o seu terminal no diretório desejado para armazenar os arquivos da aplicação, logo em seguida execute o seguinte comando :

```git clone -b dev https://github.com/YamiMaou/funcional_challenge_php.git . ```

Este comando irá clonar o **branch** **dev** do repositório da aplicação.

## Configuração

O projeto depende de alguns serviços, como o **php-fpm**, **postgres** e **nginx**, dito isso algumas portas são necessárias para que o projeto funcione corretamente, sendo elas a **8000** e **5432**.

Será necessário executar alguns comandos no seu cmd/terminal e ter o docker instalado.

No diretório raíz do projeto rode os seguintes comandos no seu cmd/terminal para criarmos o arquivo **.env** de variáveis de ambiente do projeto : 

``` mv .env.example .env ``` 

Este comando irá renomear o arquivo de exemplo existente, fazendo com que este seja o arquivo de configurações do projeto,
o mesmo já está configurado para fúncionar com o container que criaremos a seguir.

Execute o seguinte comando em seu cmd/terminal :

``` docker-compose up -d --build ```

Feito isso caso não ocorra nenhum problema, o docker irá baixar e configurar as novas dependências para que o projeto fúncione.

Agora vamos criar as tabelas em nossa base de dados, 
primeio verifique se as imagens estão sendo executadas utilizando o seguinte comando :

``` docker ps ```


Procure pelo nome **[NOME_DO_DIRETORIO]_web** ou **[NOME_DO_DIRETORIO]_web_1** na coluna **NAMES**, após validar qual o nome do container, vamos executar o seguinte comando, para atualizar as dependencias e atualizar as migrations dentro do container:

``` docker container exec [NOME_DO_DIRETORIO]_web_1 composer update ```
e 

``` docker container exec [NOME_DO_DIRETORIO]_web_1 php artisan migrate ```

Após o fim da execução, em seu client **GraphQL** preencha o endereço do servidor com o host a seguir : 

[http://localhost:8000/api](http://localhost:8000/api)

Caso não tenha um client acesse o seguinte endereço :

[http://localhost:8000/graphql-playground](http://localhost:8000/graphql-playground)

Não esqueça de preencher o campo "caminho do servidor" com o host informado acima.


Agora poderemos testar nossas requisições.

## Requisições

Minha primeira vez utilizando **GraphQL**, seguindo a sujestão do teste, obtive o seguinte resultado :

**Efetuado Depósito em conta**

Para efetuar um deposito, basta enviar obrigatóriamente os parâmetros ```conta(int)```, ```valor(float)``` para a mutation ```depositar```.

Requisição:

```
mutation {
  depositar(conta: 54321, valor: 100) {
    conta
    saldo
  }
}
```

Resposta:

```
{
  "data": {
    "depositar": {
      "conta": 54321,
      "saldo": 100
    }
  }
}
```
A mutation irá efetuar um deposito na conta informada e somar com o saldo da conta existente, caso a conta não exista, irá criar uma conta com o saldo igual ao valor informado.


**Realizando Saque de saldo quando disponível**

Para efetuar um saque, basta enviar obrigatóriamente os parâmetros ```conta(int)```, ```valor(float)``` para a mutation ```sacar```.

Requisição:
```
mutation {
  sacar(conta: 54321, valor: 80) {
    conta
    saldo
  }
}
```

Resposta:

```
{
  "data": {
    "sacar": {
      "conta": 54321,
      "saldo": 20
    }
  }
}
```
A mutation irá validar o valor do saque, caso seja menor ou igual que o saldo disponível em conta, o saldo da conta será subtraido pelo valor informado ao parâmetro, e retornará os dados atualizados.

**Realizando Saque de saldo quando indisponível**

Usando os mesmos parâmetros do exemplo acima, quando o valor informado for maior, que o saldo disponível em conta, será retornado uma menssagem de erro "Saldo insuficiênte":

Requisição:

```
mutation {
  sacar(conta: 54321, valor: 21) {
    conta
    saldo
  }
}
```
Resposta:
 
```
{
  "errors": [
    {
      "message": "Saldo insuficiente.",
      ...
    }
  ]
}
}
```

**Realizando Saque de saldo quando valor informado é inválido**

Quando o valor informado for menor que zero ou nulo, será retornado uma menssagem de erro "Valor para saque informado é inválido.":

Requisição:

```
mutation {
  sacar(conta: 54321, valor: -3) {
    conta
    saldo
  }
}
```
Resposta:
 
```
{
  "errors": [
    {
      "message": "Valor para saque informado é inválido.",
      ...
    }
  ]
}
}
```

**Consulta de Saldo**

Para efetuar a consulta de saldo, basta enviar obrigatóriamente os parâmetros ```conta(int)```, para a query ```saldo```.

Requisição:

```
query {
  saldo(conta: 54321)
}
```

Resposta:

```
{
  "data": {
    "saldo": 20
  }
}

```
# Executando Testes com PHPUnit

No diretório principal da aplicação execute o seguinte comando :

``` docker container exec [NOME_DO_DIRETORIO]_web_1 php artisan test ``` dependendo do nome do container criado no seu docker.

O PHPUnit realizará um teste na rota **/api** para verificar se a mesma está online, em seguida irá realizar os testes de transação com o GraphQL.


# Agradecimentos

[Ephyllus Oliveira](mailto:ephyllus2@gmail.com)
