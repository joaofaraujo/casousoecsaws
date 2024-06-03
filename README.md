# Requisitos

## Resumo
- Necessário criar APIs para gerenciar o cadastro de Usuários

## Endpoints
As APIs precisam expor os seguintes endpoints:

- `POST /usuario` – para criar um recurso usuário.
- `GET /usuario/[:id]` – para consultar um recurso criado com a requisição anterior.
- `GET /usuario?t=[:termo da busca]` – para fazer uma busca por usuarios.
- `GET /usuario/contagem` – endpoint especial para contagem de usuarios cadastradas.


### Criação de Usuario
`POST /usuario`

Deverá aceitar uma requisição em formato JSON com os seguintes parâmetros:

| atributo | descrição |
| --- | --- |
| **login** | obrigatório, único, string de até 32 caracteres. |
| **nome** | obrigatório, string de até 100 caracteres. |
| **nascimento** | obrigatório, string para data no formato AAAA-MM-DD (ano, mês, dia). |
| **conhecimentos** | opcional, vetor de string com cada elemento sendo obrigatório e de até 32 caracteres. |

Para requisições válidas, sua API deverá retornar status code 201 - created junto com o header "Location: /usuario/[:id]" onde [:id] é o id – em formato UUID com a versão a seu critério – do usuário que acabou de ser criada.

Exemplos de requisições válidas:
```json
{
    "login" : "joao123",
    "nome" : "João Roberto",
    "nascimento" : "2000-10-01",
    "conhecimento" : ["Area Civil", "Eletrica", "Jardinagem"]
}
```

```json
{
    "login" : "anabarb",
    "nome" : "Ana Barbosa",
    "nascimento" : "1985-09-23",
    "conhecimento" : null
}
```
Para requisições inválidas, o status code deve ser 422 - Unprocessable Entity/Content. O conteúdo do corpo deve informar o porque do erro.

Exemplos de requisições inválidas:
```json
{
    "login" : "joao123", // caso "joao123" já tenha sido criado em outra requisição
    "nome" : "João da Silva",
    "nascimento" : "2000-10-01",
    "conhecimento" : ["TI"]
}
```

```json
{
    "login" : "ana",
    "nome" : null, // não pode ser null
    "nascimento" : "1985-09-23",
    "conhecimento" : null
}
```

```json
{
    "login" : null, // não pode ser null
    "nome" : "Ana Barbosa",
    "nascimento" : "1985-01-23",
    "conhecimento" : null
}
```

Para o caso de requisições sintaticamente inválidas, a resposta deverá ter o status code para 400 - bad request. Exemplos:

```json
{
    "login" : "login único",
    "nome" : 1, // nome deve ser string e não número
    "nascimento" : "1985-01-01",
    "conhecimento" : null
}
```

```json
{
    "login" : "login único",
    "nome" : "nome",
    "nascimento" : "1985-01-01",
    "conhecimento" : [1, "PHP"] // conhecimento deve ser um array de apenas strings
}
```

### Detalhe de um Usuario
`GET /usuario/[:id]`

Deverá retornar os detalhes de um usuario caso esta tenha sido criado anteriormente. O parâmetro [:id] deve ser do tipo UUID na versão que escolher. O retorno deve ser como os exemplos a seguir.

```json
{
    "id" : "f7379ae8-8f9b-4cd5-8221-51efe19e721b",
    "login" : "jose123",
    "nome" : "José Roberto",
    "nascimento" : "2000-10-01",
    "conhecimento" : ["Engenharia Civil"]
}
```

```json
{
    "id" : "5ce4668c-4710-4cfb-ae5f-38988d6d49cb",
    "login" : "ana",
    "nome" : "Ana Barbosa",
    "nascimento" : "1985-09-23",
    "conhecimento" : null
}
```

O status code para sucesso deve ser 200 e para recursos que não existem, 404.


### Busca de Usuario
`GET /usuario?t=[:termo da busca]`

Dado o `termo da busca`, a resposta deverá ser uma lista que satisfaça o termo informado estar contido nos atributos `login`, `nome`, e/ou elementos de `conhecimento`. A busca precisa ser paginada.

O status code deverá ser sempre 200.

Exemplos: Dado os recursos seguintes existentes em sua aplicação:

```json
[{
    "id" : "f7379ae8-8f9b-4cd5-8221-51efe19e721b",
    "login" : "jose123",
    "nome" : "José Roberto",
    "nascimento" : "2000-10-01",
    "conhecimento" : ["Engenharia Civil"]
},
{
    "id" : "5ce4668c-4710-4cfb-ae5f-38988d6d49cb",
    "login" : "ana",
    "nome" : "Ana Barbosa",
    "nascimento" : "1985-09-23",
    "conhecimento" : ["TI"]
}]
```

Se a query string `t` não for informada, a resposta deve ter seu status code para 400 - bad request.

### Contagem de Usuario
`GET /usuario/contagem`

Deverá retornar em texto puro o número de registros de usuarios cadastrados.

### Database e Linguagem de Programação
Você deverá usar Postgres como base de dados e qualquer linguagem de programação a sua escolha.
