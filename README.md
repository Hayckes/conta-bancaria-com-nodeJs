## API de Contas Bancárias

O código a seguir implementa uma API de contas bancárias usando o framework Express em Node.js. Essa API permite a criação de contas, realização de depósitos, saques, consulta de extrato, atualização de informações da conta, exclusão da conta e consulta do saldo disponível.

### Endpoints da API

#### 1. Criação de Conta

- **URL:** `/account`
- **Método:** `POST`
- **Descrição:** Cria uma nova conta bancária para um cliente.
- **Corpo da requisição:**
  ```json
  {
    "cpf": "string",
    "name": "string"
  }
  ```
- **Respostas:**
  - **Código:** 201 (Criado)
  - **Código:** 400 (Bad Request)
    - **Corpo da resposta:**
      ```json
      {
        "error": "Custumer already exists"
      }
      ```

#### 2. Consulta de Extrato

- **URL:** `/statement`
- **Método:** `GET`
- **Descrição:** Retorna o extrato da conta do cliente.
- **Cabeçalho da requisição:**
  ```json
  {
    "cpf": "string"
  }
  ```
- **Respostas:**
  - **Código:** 200 (OK)
    - **Corpo da resposta:**
      ```json
      [
        {
          "description": "string",
          "amount": number,
          "created_at": "string",
          "type": "credit"|"debit"
        },
        ...
      ]
      ```

#### 3. Depósito

- **URL:** `/deposit`
- **Método:** `POST`
- **Descrição:** Realiza um depósito na conta do cliente.
- **Cabeçalho da requisição:**
  ```json
  {
    "cpf": "string"
  }
  ```
- **Corpo da requisição:**
  ```json
  {
    "description": "string",
    "amount": number
  }
  ```
- **Respostas:**
  - **Código:** 201 (Criado)

#### 4. Saque

- **URL:** `/withdraw`
- **Método:** `POST`
- **Descrição:** Realiza um saque na conta do cliente.
- **Cabeçalho da requisição:**
  ```json
  {
    "cpf": "string"
  }
  ```
- **Corpo da requisição:**
  ```json
  {
    "amount": number
  }
  ```
- **Respostas:**
  - **Código:** 201 (Criado)
  - **Código:** 400 (Bad Request)
    - **Corpo da resposta:**
      ```json
      {
        "error": "Insufficient funds!"
      }
      ```

#### 5. Consulta de Extrato por Data

- **URL:** `/statement/date`
- **Método:** `GET`
- **Descrição:** Retorna o extrato da conta do cliente com base em uma data específica.
- **Cabeçalho da requisição:**
  ```json
  {
    "cpf": "string"
  }
  ```
- **Parâmetros de consulta:**
  ```
  date: string (formato: 'YYYY-MM-DD')
  ```
- **Respostas:**
  - **Código:** 200 (OK)
    - \*\*Cor

po da resposta:\*\*
`json
      [
        {
          "description": "string",
          "amount": number,
          "created_at": "string",
          "type": "credit"|"debit"
        },
        ...
      ]
      `

#### 6. Atualização de Informações da Conta

- **URL:** `/account`
- **Método:** `PUT`
- **Descrição:** Atualiza as informações da conta do cliente.
- **Cabeçalho da requisição:**
  ```json
  {
    "cpf": "string"
  }
  ```
- **Corpo da requisição:**
  ```json
  {
    "name": "string"
  }
  ```
- **Respostas:**
  - **Código:** 201 (Criado)

#### 7. Consulta de Informações da Conta

- **URL:** `/account`
- **Método:** `GET`
- **Descrição:** Retorna as informações da conta do cliente.
- **Cabeçalho da requisição:**
  ```json
  {
    "cpf": "string"
  }
  ```
- **Respostas:**
  - **Código:** 200 (OK)
    - **Corpo da resposta:**
      ```json
      {
        "cpf": "string",
        "name": "string",
        "id": "string",
        "statement": [
          {
            "description": "string",
            "amount": number,
            "created_at": "string",
            "type": "credit"|"debit"
          },
          ...
        ]
      }
      ```

#### 8. Exclusão de Conta

- **URL:** `/account`
- **Método:** `DELETE`
- **Descrição:** Exclui a conta do cliente.
- **Cabeçalho da requisição:**
  ```json
  {
    "cpf": "string"
  }
  ```
- **Respostas:**
  - **Código:** 200 (OK)
    - **Corpo da resposta:**
      ```json
      {
        "cpf": "string",
        "name": "string",
        "id": "string",
        "statement": [
          {
            "description": "string",
            "amount": number,
            "created_at": "string",
            "type": "credit"|"debit"
          },
          ...
        ]
      }
      ```

#### 9. Consulta de Saldo

- **URL:** `/balance`
- **Método:** `GET`
- **Descrição:** Retorna o saldo disponível na conta do cliente.
- **Cabeçalho da requisição:**
  ```json
  {
    "cpf": "string"
  }
  ```
- **Respostas:**
  - **Código:** 200 (OK)
    - **Corpo da resposta:**
      ```json
      {
        "balance": number
      }
      ```

### Configuração e Execução do Servidor

O código abaixo mostra a configuração e a execução do servidor Express.

```javascript
const express = require("express");
const { v4: uuidv4 } = require("uuid");

const app = express();

app.use(express.json());

const customers = [];

// ... implementação dos middlewares e dos endpoints ...

app.listen(3031);
```

### Middlewares

#### 1. Verificação da Existência da Conta pelo CPF

O middleware `verifyExistsAccountCPF` é responsável por verificar se a conta do cliente existe com base no CPF fornecido nos cabe

çalhos das requisições. Ele também adiciona o objeto do cliente à requisição para uso nos outros endpoints.

```javascript
function verifyExistsAccountCPF(request, response, next) {
  const { cpf } = request.headers;

  const customer = customers.find((customer) => customer.cpf === cpf);

  if (!customer) {
    return response.status(400).json({ error: "Customer not find" });
  }

  request.customer = customer;

  return next();
}
```

### Funcionalidades Auxiliares

#### 1. Cálculo do Saldo

A função `getBalance` realiza o cálculo do saldo com base nas operações registradas no extrato do cliente.

```javascript
function getBalance(statement) {
  const balance = statement.reduce((acc, operation) => {
    if (operation.type === "credit") {
      return acc + operation.amount;
    } else {
      return acc - operation.amount;
    }
  }, 0);
  return balance;
}
```

### Considerações Finais

O código acima implementa uma API básica de contas bancárias com funcionalidades de criação de conta, depósito, saque, consulta de extrato, atualização de informações da conta, exclusão de conta e consulta de saldo. Certifique-se de ter o Node.js e o pacote Express instalados para executar o servidor corretamente.
