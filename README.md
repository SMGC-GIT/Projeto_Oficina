# 🍺 DESAFIO DE PROJETO DIO x HEINEKEN  
## 📦 Projeto de Banco de Dados Relacional | Oficina  

---

## 📝 Descrição do Projeto  
Este projeto modela o banco de dados relacional de uma oficina mecânica, abordando desde a modelagem lógica até a implementação de queries SQL complexas, com foco em gestão de clientes, veículos, ordens de serviço, serviços prestados, mecânicos e pagamentos.  

>**Foco Principal:** Demonstrar domínio de SQL (DDL + DML + consultas), modelagem relacional e organização de projetos de dados para fins de portfólio.  

---

## 📌 Objetivos  
- Estruturar o banco de dados da oficina de forma normalizada.  
- Criar e popular tabelas com dados realistas.  
- Executar consultas SQL úteis e analíticas.  
- Visualizar a estrutura através de um diagrama ER.  

---

## 🧠 Modelagem Lógica  

**Entidades:**  
- `clientes`  
- `veiculos`  
- `mecanicos`  
- `servicos`  
- `ordens_servico`  
- `itens_ordem_servico`  
- `pagamentos`  

**Principais Relacionamentos:**  
- `Um cliente pode ter vários veículos` 
- `Uma ordem de serviço envolve um cliente, um veículo e um mecânico`  
- `Uma ordem pode conter vários serviços`  
- `Uma ordem pode ter um ou nenhum pagamento`  

---

## 🖼️ Diagrama ER  

> 🔗 **[Clique aqui para visualizar o Diagrama do Banco de Dados](https://dbdiagram.io/)**  

---

## 🗃️ Estrutura do Banco de Dados (DDL)

```sql
CREATE TABLE clientes (
    id_cliente SERIAL PRIMARY KEY,
    nome VARCHAR(100),
    telefone VARCHAR(20),
    email VARCHAR(100),
    endereco TEXT
);

CREATE TABLE veiculos (
    id_veiculo SERIAL PRIMARY KEY,
    id_cliente INT REFERENCES clientes(id_cliente),
    marca VARCHAR(50),
    modelo VARCHAR(50),
    ano INT,
    placa VARCHAR(10)
);

CREATE TABLE mecanicos (
    id_mecanico SERIAL PRIMARY KEY,
    nome VARCHAR(100),
    especialidade VARCHAR(100),
    telefone VARCHAR(20)
);

CREATE TABLE servicos (
    id_servico SERIAL PRIMARY KEY,
    descricao VARCHAR(200),
    preco_padrao NUMERIC(10,2)
);

CREATE TABLE ordens_servico (
    id_ordem SERIAL PRIMARY KEY,
    id_cliente INT REFERENCES clientes(id_cliente),
    id_veiculo INT REFERENCES veiculos(id_veiculo),
    id_mecanico INT REFERENCES mecanicos(id_mecanico),
    data_emissao DATE,
    status VARCHAR(30),
    valor_total NUMERIC(10,2)
);

CREATE TABLE itens_ordem_servico (
    id_item SERIAL PRIMARY KEY,
    id_ordem INT REFERENCES ordens_servico(id_ordem),
    id_servico INT REFERENCES servicos(id_servico),
    quantidade INT,
    preco_unitario NUMERIC(10,2),
    subtotal NUMERIC(10,2)
);

CREATE TABLE pagamentos (
    id_pagamento SERIAL PRIMARY KEY,
    id_ordem INT REFERENCES ordens_servico(id_ordem),
    data_pagamento DATE,
    forma_pagamento VARCHAR(50),
    valor_pago NUMERIC(10,2),
    status_pagamento VARCHAR(30)
);
```

---

## 📥 Inserção de Dados (DML)


### Clientes
```sql
INSERT INTO clientes (nome, telefone, email, endereco) VALUES
('Carlos Souza', '11999999999', 'carlos@email.com', 'Rua A, 123'),
('Maria Oliveira', '11988888888', 'maria@email.com', 'Av. B, 456');
```

### Veículos
```sql
INSERT INTO veiculos (id_cliente, marca, modelo, ano, placa) VALUES
(1, 'Toyota', 'Corolla', 2018, 'ABC1D23'),
(2, 'Honda', 'Civic', 2020, 'XYZ9Z88');
```

### Mecânicos
```sql
INSERT INTO mecanicos (nome, especialidade, telefone) VALUES
('João Mecânico', 'Motor', '11977777777'),
('Ana Mecânica', 'Suspensão', '11966666666');
```

### Serviços
```sql
INSERT INTO servicos (descricao, preco_padrao) VALUES
('Troca de óleo', 150.00),
('Alinhamento e balanceamento', 120.00),
('Revisão de freios', 200.00);
```

### Ordens de Serviço
```sql
INSERT INTO ordens_servico (id_cliente, id_veiculo, id_mecanico, data_emissao, status, valor_total) VALUES
(1, 1, 1, '2024-04-01', 'Concluída', 270.00),
(2, 2, 2, '2024-04-03', 'Em andamento', 200.00);
```

### Itens da Ordem
```sql
INSERT INTO itens_ordem_servico (id_ordem, id_servico, quantidade, preco_unitario, subtotal) VALUES
(1, 1, 1, 150.00, 150.00),
(1, 2, 1, 120.00, 120.00),
(2, 3, 1, 200.00, 200.00);
```

### Pagamentos
```sql
INSERT INTO pagamentos (id_ordem, data_pagamento, valor_pago, forma_pagamento, status_pagamento) VALUES
(1, '2025-04-01', 150.00, 'Cartão de Crédito', 'Aprovado'),
(2, NULL, 0.00, 'PIX', 'Pendente'),
(1, '2025-04-03', 0.00, 'Boleto', 'Cancelado');
```


---

## 📊 Consultas SQL Avançadas

### 1. Faturamento Mensal

```sql
SELECT TO_CHAR(data_emissao, 'YYYY-MM') AS mes, SUM(valor_total) AS faturamento
FROM ordens_servico
GROUP BY TO_CHAR(data_emissao, 'YYYY-MM')
ORDER BY mes;

Resultado esperado:


| mes      | faturamento |
|----------|-------------|
| 2024-04  | 470.00      |
```

### 2. Clientes que mais gastaram

```sql
SELECT c.nome, SUM(os.valor_total) AS total_gasto
FROM clientes c
JOIN ordens_servico os ON c.id_cliente = os.id_cliente
GROUP BY c.nome
ORDER BY total_gasto DESC;

Resultado esperado:

| nome           | total_gasto |
|----------------|-------------|
| Carlos Souza   | 270.00      |
| Maria Oliveira | 200.00      |
```

### 3. Serviços mais realizados

```sql
SELECT s.descricao, COUNT(*) AS total_servicos
FROM itens_ordem_servico ios
JOIN servicos s ON ios.id_servico = s.id_servico
GROUP BY s.descricao
ORDER BY total_servicos DESC;

Resultado esperado:

| descricao                    | total_servicos |
|------------------------------|----------------|
| Troca de óleo                | 1              |
| Alinhamento e balanceamento  | 1              |
| Revisão de freios            | 1              |
```

### 4. Ordens com seus serviços

```sql
SELECT os.id_ordem, c.nome AS cliente, s.descricao AS servico, ios.quantidade, ios.subtotal
FROM ordens_servico os
JOIN clientes c ON os.id_cliente = c.id_cliente
JOIN itens_ordem_servico ios ON os.id_ordem = ios.id_ordem
JOIN servicos s ON ios.id_servico = s.id_servico;

Resultado esperado:

| id_ordem | cliente        | servico                     | quantidade | subtotal |
|----------|----------------|-----------------------------|------------|----------|
| 1        | Carlos Souza   | Troca de óleo               | 1          | 150.00   |
| 1        | Carlos Souza   | Alinhamento e balanceamento | 1          | 120.00   |
| 2        | Maria Oliveira | Revisão de freios           | 1          | 200.00   |
```

### 5. Pagamentos pendentes ou não aprovados

```sql
SELECT os.id_ordem, c.nome, os.valor_total, p.status_pagamento
FROM ordens_servico os
JOIN clientes c ON os.id_cliente = c.id_cliente
LEFT JOIN pagamentos p ON os.id_ordem = p.id_ordem
WHERE p.status_pagamento IS NULL OR p.status_pagamento <> 'Aprovado';

Resultado esperado:

| id_ordem | nome           | valor_total | status_pagamento |
|----------|----------------|-------------|------------------|
| 1        | Carlos Souza   | 270.00      | Cancelado        |
| 2        | Maria Oliveira | 200.00      | Pendente         |
```

---

## 📌 Créditos e Contato

> Criado por **SILVIA GUIMARÃES** como parte de portfólio de projetos SQL e modelagem relacional.

Para dúvidas ou sugestões, entre em contato comigo:
- **E-mail:** (sguimaraes1004@gmail.com)
- **Redes Sociais: [LinkedIn](https://www.linkedin.com/in/silvia-maria-guimar%C3%A3es-costa-3a01b423b)**
  
---

## **Agradecimentos**

Agradeço à equipe da **DIO** e **HEINEKEN** pela oportunidade de participar deste desafio e ampliar minhas habilidades em modelagem de banco de dados e organização estrutural de informações.  
Este projeto reflete o aprendizado prático e meu compromisso com boas práticas na área de tecnologia.

---

🍺 _A parceria com a Heineken reforça o compromisso de promover a inovação e o aprendizado na área de tecnologia._

---


# ![DIO Logo](https://hermes.digitalinnovation.one/assets/diome/logo.png)
