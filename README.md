# Desafio Back-end Caleta (Pleno)

## Antes de começar

- Inicie criando um repositório no seu Github.
- Faça todos os commits referentes a esse desafio no seu repositório.
- Quando terminar, envie o link do repositório ao nosso e-mail.
- Se tiver qualquer dúvida durante o desafio, não hesite em nos perguntar.

## Sobre o ambiente da aplicação

Você pode escolher a linguagem de programação e o framework que preferir para desenvolver a aplicação deste teste. Trabalhe com aquilo que você se sentirá confortável em discutir durante a entrevista.

---

## Introdução

Neste desafio, você representará um cassino que está integrando na nossa API. Você irá criar uma API para simular a carteira digital deste cassino, que deverá receber transações do fluxo de jogo dos jogadores. A API deverá ser capaz de gerenciar o saldo de um jogador, e também permitir apostas e ganhos.

### Requisitos:

- As respostas da API devem ser no formato **JSON**.  
- A aplicação deve ser containerizada usando **Docker**.  
- A Api deve estar documentada.  
- A Api deve ter testes comprovando o correto funcionamento. 

A **API** deve possuir, no mínimo, os seguintes endpoints:

#### GET /balance

Este endpoint será utilizado para consultar o saldo da carteira digital de um jogador. Ele deverá receber como parâmetro na URL o ID do jogador (por exemplo, `player=1`).

Por exemplo:

```
Method: GET 
Url: localhost:8000/balance?player=1
```
Reposta:
```
HTTP Status: 200
Body: {
    "player": 1,
    "balance": 1000
}
```

exemplo 2:

```
Method: GET 
Url: localhost:8000/balance/1
```
Reposta:
```
HTTP Status: 200
Body {
    "player": 1,
    "balance": 1000
}
```

#### POST /bet

Este endpoint será utilizado para realizar apostas usando a carteira digital de um jogador. Ele deverá receber no corpo da requisição um objeto JSON contendo o ID do jogador e o valor a ser sacado, juntamente com o UUID da Transação (Unico e Gerado pelo usuario).  
Como resposta, deverá retornar o saldo atualizado da carteira digital e o UUID da transação.
Caso Receba uma UUID ja cadastrado deve retornar a mesma resposta já respondida.(Idempotencia)

Por exemplo:

```
Method: POST
Url: localhost:8000/bet
Body: {
    "player": 1,
    "value": 5,
    "txn_uuid": "57fc384b-645d-44de-8c36-dc9d7fcc35c4"
}
```

*Resposta:* 
```
HTTP Status: 200
Body: {
    player": 1,
    "balance": 995,
    "txn_uuid": "57fc384b-645d-44de-8c36-dc9d7fcc35c4"
}
```

#### POST /win

Este endpoint será utilizado para realizar ganhos usando a carteira digital de um jogador. Ele deverá receber no corpo da requisição um objeto JSON contendo o ID do jogador e o valor a ser depositado, juntamente com UUID da Transação (Unico e Gerado pelo usuario).  
Como resposta, deverá retornar o saldo atualizado da carteira digital e o UUID da transação.
Caso Receba uma UUID ja cadastrado deve retornar a mesma resposta já respondida.(Idempotencia)

Por exemplo:

*Requisição:* 
```
Method: POST
Url: localhost:8000/win
Body: {
    "player": 1,
    "value": 1000,
    "txn_uuid": "25a894c8-83c5-4e50-9427-911ba87a2aaa"
}
```

*Resposta:* 
```
HTTP Status: 200
Body: {
    "player":1,
    "balance": 1995,
    "txn_uuid": "25a894c8-83c5-4e50-9427-911ba87a2aaa"
}
```

#### POST /rollback

Este endpoint será utilizado para realizar o cancelamento de transações, sendo as transações possiveis de cancelar Apenas as de **Aposta**. Ele deverá receber no corpo da requisição um objeto JSON contendo o apenas o UUID da Transação (Unico e Gerado pelo usuario no Momento da aposta).  
Como resposta, deverá retornar o saldo atualizado da carteira digital e o UUID da transação.
Caso Receba uma UUID de uma transação ja cadastrada deve Cancela-la e devolver o dinheiro ao jogador, Caso a Transação não esteja cadastrada, deve armazenar que a transação foi Cancelada, e no momento que receber uma transação de **Aposta** com o mesmo UUID deve retornar um Erro.

Por exemplo:

*Requisição:* 
```
Method: POST
Url: localhost:8000/rollback
Body: {
    "txn_uuid": "57fc384b-645d-44de-8c36-dc9d7fcc35c4"
}
```

*Resposta:* 
```
HTTP Status: 200
Body: {
    "player":1,
    "balance": 2000
}
```

### Validações e Erros

- As respostas de erro devem ser detalhadas e no formato JSON.
- Implementar validações para assegurar que o valor da aposta, do ganho e da transferência não sejam negativos.
- Validar se o jogador existe antes de realizar qualquer operação.
- Implementar limites para as apostas, ganhos e transferências, e validar esses limites.
- Implementar transações em operações críticas para garantir a consistência dos dados.
- Implementar rollback automático em caso de falhas.

### Persistência dos Dados

- Utilizar um banco de dados relacional.

### Containerização

- Containerização completa da aplicação usando Docker.
- Containerização de qualquer dependencia como Banco de Dados.

### Documentação e Testes

- Documentar a API usando ferramentas como Swagger, ou também pode ser documentada usando apenas MarkDown.
- Implementar testes automatizados, incluindo testes de integração e testes de ponta a ponta.

### Sobre a criação do Player

- Qualquer metodo de criação será aceito, des de que consigamos testar a aplicação.
- Exemplo: Criar por endpoint ou executar um command, ou qualquer outra maneira desde que documentado o passo a passo para conseguirmos reproduzir.

### Opcionais:

O Endpoint abaixo é apenas opcional e não é necessario a implementação.


#### GET /player/history

Este endpoint será utilizado para retornar o histórico de transações de um jogador. Ele deverá receber como parâmetro na URL o ID do jogador.

Por exemplo:

*Requisição:* 
```
Method: GET
Url: localhost:8000/player/history?player=1
```

ou

```
Method: GET
Url: localhost:8000/player/1/history
```

*Resposta:* 
```
{
    "player": 1,
    "history": [
        {
            "txn_uuid": "57fc384b-645d-44de-8c36-dc9d7fcc35c4",
            "type": "bet",
            "value": 5,
            "rolled_back": True
        },
        {
            "txn_uuid": "25a894c8-83c5-4e50-9427-911ba87a2aaa",
            "type": "win",
            "value": 1000
        },

        {
            "txn_uuid": "e0f7b961-69d4-4f4c-82e5-c1568555ea4e",
            "type": "bet",
            "value": 20,
            "rolled_back": False
        },
    ]
}
```

---

## O que será avaliado

- Organização do código;
- Tratamento de erros;
- Persistência dos dados;
- Validações e respostas de erro;
- Documentação da API;
- Implementação de testes automatizados (unitários, de integração e de carga);
- Uso de práticas de autenticação e autorização;
- Containerização.

## O que não será avaliado

- Segurança avançada da aplicação;
- Escalabilidade.
- Apesar de necessario para a Aplicação a parte de criação de Player não será avaliada.
- A formatação, e mensagens de erros desde que elas existam.

---
