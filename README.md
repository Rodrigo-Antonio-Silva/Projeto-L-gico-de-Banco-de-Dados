# Projeto-Logico-de-Banco-de-Dados
Primeiro Projeto Lógico de Banco de Dados do Bootcamp DIO Suzano Análise de dados

# 📦 Banco de Dados E-Commerce

Este repositório contém o script SQL para a criação do banco de dados **E-Commerce**, incluindo todas as tabelas e suas relações.

## 🏗️ Estrutura do Banco de Dados

### 📍 Criando o banco de dados
```sql
CREATE DATABASE ecommerce;
USE ecommerce;

🛍️ Tabela CLIENTE
CREATE TABLE client (
    idClient INT AUTO_INCREMENT PRIMARY KEY,
    fName VARCHAR(12) NOT NULL,
    mName VARCHAR(12),
    lName VARCHAR(12),
    birthday DATE,
    tel VARCHAR(20),
    email VARCHAR(45) NOT NULL,
    ssn CHAR(11) NOT NULL UNIQUE,
    address VARCHAR(45),
    typeClient ENUM('normal', 'elite', 'super') NOT NULL DEFAULT 'normal'
);

🏢 Tabela FORNECEDOR (SUPLIER)
CREATE TABLE suplier (
    idSuplier INT AUTO_INCREMENT PRIMARY KEY,
    suplierName VARCHAR(45) NOT NULL,
    ssn CHAR(11) NOT NULL UNIQUE,
    address VARCHAR(45) NOT NULL
);

📦 Tabela PRODUTO
CREATE TABLE product (
    idProduct INT AUTO_INCREMENT PRIMARY KEY,
    idProductSuplier INT,
    productName VARCHAR(45) NOT NULL,
    descr VARCHAR(45),
    category ENUM('eletrônico', 'vestimenta', 'brinquedo', 'alimentos') NOT NULL,
    CONSTRAINT fk_product_suplier FOREIGN KEY (idProductSuplier) REFERENCES suplier(idSuplier)
);

💰 Tabela PREÇO
CREATE TABLE price (
    idProduct INT PRIMARY KEY,
    price DECIMAL(10,2) NOT NULL,
    discount DECIMAL(10,2) DEFAULT 0.00,
    finalPrice DECIMAL(10,2) NOT NULL,
    CONSTRAINT fk_product_price FOREIGN KEY (idProduct) REFERENCES product(idProduct)
);

🏬 Tabela ESTOQUE
CREATE TABLE storage (
    idStorage INT AUTO_INCREMENT PRIMARY KEY,
    idStorageProduct INT NOT NULL,
    quantity INT NOT NULL,
    dateIN DATE NOT NULL,
    dateOUT DATE NULL,
    CONSTRAINT fk_storage_product FOREIGN KEY (idStorageProduct) REFERENCES product(idProduct)
);

🧑‍💼 Tabela VENDEDORES
CREATE TABLE seller (
    idSeller INT AUTO_INCREMENT PRIMARY KEY,
    sellerName VARCHAR(30) NOT NULL,
    ssn CHAR(11) NOT NULL UNIQUE,
    store VARCHAR(30) NOT NULL,
    email VARCHAR(30) NOT NULL
);

📦 Tabela DISPONIBILIDADE DE PRODUTOS (FORNECEDORES E PRODUTOS)
CREATE TABLE avaiable (
    idAvaiable INT AUTO_INCREMENT PRIMARY KEY,
    idSuplier INT NOT NULL,
    idProduct INT NOT NULL,
    CONSTRAINT avaiable_suplier_fk FOREIGN KEY (idSuplier) REFERENCES suplier(idSuplier),
    CONSTRAINT avaiable_product_fk FOREIGN KEY (idProduct) REFERENCES product(idProduct)
);

📦 Tabela PEDIDOS
CREATE TABLE T_order (
    idOrder INT AUTO_INCREMENT PRIMARY KEY,
    idOrderClient INT NOT NULL,
    orderDate DATE NOT NULL,
    Total DECIMAL(10,2),
    orderStatus ENUM('em andamento', 'concluído', 'aberto', 'fechado', 'cancelado') NOT NULL DEFAULT 'em andamento',
    CONSTRAINT fk_order_client FOREIGN KEY (idOrderClient) REFERENCES client(idClient)
);

📦 Tabela PRODUTO_PEDIDO
CREATE TABLE product_order (
    idProduct INT NOT NULL,
    idOrder INT NOT NULL,
    productOrderQTD INT NOT NULL CHECK (productOrderQTD > 0),
    productOrderStatus ENUM('aberto', 'em andamento', 'concluído', 'fechado') NOT NULL DEFAULT 'em andamento',
    PRIMARY KEY (idProduct, idOrder),
    CONSTRAINT prod_order_fk FOREIGN KEY (idProduct) REFERENCES product(idProduct),
    CONSTRAINT order_product_fk FOREIGN KEY (idOrder) REFERENCES T_order(idOrder)
);

💰 Tabela FATURAS
CREATE TABLE invoice (
    idInvoice INT AUTO_INCREMENT PRIMARY KEY,
    idOrderInvoice INT NOT NULL,
    PaymentMode ENUM('cash', 'card', 'bill') NOT NULL DEFAULT 'cash',
    invoiceDate DATE NOT NULL,
    CONSTRAINT fk_order_invoice FOREIGN KEY (idOrderInvoice) REFERENCES T_order(idOrder)
);

🚚 Tabela FRETE
CREATE TABLE freight (
    idfreight INT AUTO_INCREMENT PRIMARY KEY,
    idfreightInvoice INT NOT NULL,
    freightType VARCHAR(25) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    freightStatus ENUM('pendente', 'enviado', 'entregue') NOT NULL DEFAULT 'pendente',
    date DATE NOT NULL,
    estimateDelivere DATE NOT NULL,
    codTracking VARCHAR(12) NOT NULL,
    CONSTRAINT fk_freight_Invoice FOREIGN KEY (idfreightInvoice) REFERENCES Invoice(idInvoice)
);

🏬 Tabela PRODUTO_ESTOQUE
CREATE TABLE product_storage (
    idProduct INT NOT NULL,
    idStorage INT NOT NULL,
    productStorageQTD INT NOT NULL CHECK (productStorageQTD >= 0),
    PRIMARY KEY (idProduct, idStorage),
    CONSTRAINT prod_storage_fk FOREIGN KEY (idProduct) REFERENCES product(idProduct),
    CONSTRAINT storage_prod_fk FOREIGN KEY (idStorage) REFERENCES storage(idStorage)
);

## Queries

1️⃣ Recuperações simples com SELECT

Recuperar todos os clientes cadastrados:
select * from client;

1	Carlos	Eduardo	Silva	1985-03-15	11987654321	carlos.silva@email.com	12345678901	Rua das Flores, 123	normal
2	Mariana	Aparecida	Santos	1990-07-22	21987654321	mariana.santos@email.com	98765432100	Av. Paulista, 456	elite
3	Ricardo	José	Pereira	1982-12-10	31987654321	ricardo.pereira@email.com	11122233344	Rua da Paz, 789	super
4	Fernanda	Cristina	Oliveira	1995-05-05	41987654321	fernanda.oliveira@email.com	55566677788	Travessa da Liberdade, 101	normal
5	João	Pedro	Almeida	1988-09-18	51987654321	joao.almeida@email.com	99988877766	Rua do Comércio, 202	elite
6	Beatriz	Fernandes	Melo	1992-11-30	61987654321	beatriz.melo@email.com	33322211100	Alameda dos Anjos, 303	super
7	Lucas	Henrique	Rocha	1993-04-25	71987654321	lucas.rocha@email.com	77788899911	Rua São José, 404	normal
8	Amanda	Gomes	Barros	1987-06-12	81987654321	amanda.barros@email.com	22233344455	Praça das Estrelas, 505	elite
9	Gustavo	César	Farias	1996-08-07	91987654321	gustavo.farias@email.com	66655544433	Av. Central, 606	super
10	Larissa	Maria	Carvalho	1984-01-29	11999998888	larissa.carvalho@email.com	44455566622	Rua Nova, 707	normal
11	Thiago	Antônio	Lima	1989-02-14	12987654321	thiago.lima@email.com	88877766655	Estrada Velha, 808	elite

Recuperar os nomes dos produtos e seus preços:

Smartphone X10	3999.99
Jaqueta de Couro	249.90
Boneca Fashion	149.50
Chocolate ao Leite	599.90
Notebook Ultra	4499.99
Tênis Esportivo	999.00
Carrinho de Controle	199.99
Cereal Matinal	299.50
Fone Bluetooth	7199.00
Camisa Polo	99.00
Ursinho de Pelúcia	1500.00

2️⃣ Filtros com WHERE
Recuperar todos os pedidos concluídos:

SELECT * FROM T_order WHERE orderStatus = 'concluído';

1	1	2025-01-05	3899.99	concluído
4	4	2025-01-08	559.90	concluído
7	7	2025-01-11	149.99	concluído
9	9	2025-01-13	6999.00	concluído

Recuperar todos os produtos da categoria "eletrônico":

1	1	Smartphone X10	Celular 128GB, 6GB RAM	eletrônico
5	1	Notebook Ultra	Intel i7, 16GB RAM	eletrônico
9	5	Fone Bluetooth	Cancelamento de ruído	eletrônico

3️⃣ Criar expressões para gerar atributos derivados
Criar uma coluna com o preço final com desconto aplicado:

SELECT idProduct, price, discount, (price - discount) AS finalPrice 
FROM price;

1	3999.99	100.00	3899.99
2	249.90	50.00	199.90
3	149.50	20.00	129.50
4	599.90	40.00	559.90
5	4499.99	200.00	4299.99
6	999.00	100.00	899.00
7	199.99	50.00	149.99
8	299.50	50.00	249.50
9	7199.00	200.00	6999.00
10	99.00	12.00	87.00
11	1500.00	300.00	1200.00

Criar uma coluna com o nome completo dos clientes:

SELECT idClient, CONCAT(fName, ' ', mName, ' ', lName) AS fullName 
FROM client;

1	Carlos Eduardo Silva
2	Mariana Aparecida Santos
3	Ricardo José Pereira
4	Fernanda Cristina Oliveira
5	João Pedro Almeida
6	Beatriz Fernandes Melo
7	Lucas Henrique Rocha
8	Amanda Gomes Barros
9	Gustavo César Farias
10	Larissa Maria Carvalho
11	Thiago Antônio Lima

4️⃣ Definir ordenações dos dados com ORDER BY

Ordenar os produtos do mais caro para o mais barato:

SELECT productName, price FROM product p 
JOIN price pr ON p.idProduct = pr.idProduct
ORDER BY price DESC;

Fone Bluetooth	7199.00
Notebook Ultra	4499.99
Smartphone X10	3999.99
Ursinho de Pelúcia	1500.00
Tênis Esportivo	999.00
Chocolate ao Leite	599.90
Cereal Matinal	299.50
Jaqueta de Couro	249.90
Carrinho de Controle	199.99
Boneca Fashion	149.50
Camisa Polo	99.00

Ordenar os clientes pelo nome em ordem alfabética:

SELECT * FROM client ORDER BY fName ASC;

8	Amanda	Gomes	Barros	1987-06-12	81987654321	amanda.barros@email.com	22233344455	Praça das Estrelas, 505	elite
6	Beatriz	Fernandes	Melo	1992-11-30	61987654321	beatriz.melo@email.com	33322211100	Alameda dos Anjos, 303	super
1	Carlos	Eduardo	Silva	1985-03-15	11987654321	carlos.silva@email.com	12345678901	Rua das Flores, 123	normal
4	Fernanda	Cristina	Oliveira	1995-05-05	41987654321	fernanda.oliveira@email.com	55566677788	Travessa da Liberdade, 101	normal
9	Gustavo	César	Farias	1996-08-07	91987654321	gustavo.farias@email.com	66655544433	Av. Central, 606	super
5	João	Pedro	Almeida	1988-09-18	51987654321	joao.almeida@email.com	99988877766	Rua do Comércio, 202	elite
10	Larissa	Maria	Carvalho	1984-01-29	11999998888	larissa.carvalho@email.com	44455566622	Rua Nova, 707	normal
7	Lucas	Henrique	Rocha	1993-04-25	71987654321	lucas.rocha@email.com	77788899911	Rua São José, 404	normal
2	Mariana	Aparecida	Santos	1990-07-22	21987654321	mariana.santos@email.com	98765432100	Av. Paulista, 456	elite
3	Ricardo	José	Pereira	1982-12-10	31987654321	ricardo.pereira@email.com	11122233344	Rua da Paz, 789	super
11	Thiago	Antônio	Lima	1989-02-14	12987654321	thiago.lima@email.com	88877766655	Estrada Velha, 808	elite

5️⃣ Condições de filtros aos grupos – HAVING

Mostrar apenas categorias de produtos que tenham mais de 2 produtos cadastrados:

SELECT category, COUNT(*) AS totalProdutos
FROM product
GROUP BY category
HAVING COUNT(*) > 2;

eletrônico	3
vestimenta	3
brinquedo	3

Listar os vendedores que venderam mais de 20 produtos no total:

SELECT sp.seller_idSeller, s.sellerName, SUM(sp.sellQTD) AS totalVendas
FROM seller_product sp
JOIN seller s ON sp.seller_idSeller = s.idSeller
GROUP BY sp.seller_idSeller, s.sellerName
HAVING SUM(sp.sellQTD) > 20;

13	Mariana Souza	30
14	Ricardo Lima	25
15	Fernanda Oliveira	50
19	Amanda Barros	22
21	Larissa Carvalho	35
22	Thiago Lima	40

6️⃣ Criar junções (JOIN) entre tabelas

Recuperar pedidos com detalhes dos clientes:

SELECT o.idOrder, c.fName, c.lName, o.orderDate, o.Total, o.orderStatus
FROM T_order o
JOIN client c ON o.idOrderClient = c.idClient;

1	Carlos	Silva	2025-01-05	3899.99	concluído
2	Mariana	Santos	2025-01-06	199.90	em andamento
3	Ricardo	Pereira	2025-01-07	1299.50	aberto
4	Fernanda	Oliveira	2025-01-08	559.90	concluído
5	João	Almeida	2025-01-09	4299.99	fechado
6	Beatriz	Melo	2025-01-10	899.00	cancelado
7	Lucas	Rocha	2025-01-11	149.99	concluído
8	Amanda	Barros	2025-01-12	249.50	em andamento
9	Gustavo	Farias	2025-01-13	6999.00	concluído
10	Larissa	Carvalho	2025-01-14	87.00	aberto
11	Thiago	Lima	2025-01-15	1200.00	fechado

Recuperar produtos vendidos e seus respectivos vendedores:

SELECT p.productName, s.sellerName, sp.sellQTD
FROM seller_product sp
JOIN product p ON sp.product_idProduct = p.idProduct
JOIN seller s ON sp.seller_idSeller = s.idSeller;

Smartphone X10	Carlos Andrade	15
Jaqueta de Couro	Mariana Souza	30
Boneca Fashion	Ricardo Lima	25
Chocolate ao Leite	Fernanda Oliveira	50
Notebook Ultra	João Almeida	10
Tênis Esportivo	Beatriz Melo	20
Carrinho de Controle	Lucas Rocha	18
Cereal Matinal	Amanda Barros	22
Fone Bluetooth	Gustavo Farias	12
Camisa Polo	Larissa Carvalho	35
Ursinho de Pelúcia	Thiago Lima	40

Recuperar detalhes das faturas e seus respectivos pedidos:

SELECT i.idInvoice, o.idOrder, o.orderDate, i.PaymentMode, i.invoiceDate
FROM invoice i
JOIN T_order o ON i.idOrderInvoice = o.idOrder;

1	1	2025-01-05	card	2025-01-06
2	2	2025-01-06	cash	2025-01-07
3	3	2025-01-07	bill	2025-01-08
4	4	2025-01-08	card	2025-01-09
5	5	2025-01-09	cash	2025-01-10
6	6	2025-01-10	bill	2025-01-11
7	7	2025-01-11	card	2025-01-12
8	8	2025-01-12	cash	2025-01-13
9	9	2025-01-13	bill	2025-01-14
10	10	2025-01-14	card	2025-01-15
11	11	2025-01-15	cash	2025-01-16

🛠 Como Usar
1️⃣ Clone o repositório:

git clone https://github.com/seu-usuario/ecommerce-db.git

📌 Agora seu banco de dados está pronto para ser usado! 🚀


									



									
									
									
