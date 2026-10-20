# Baozi Store API 🥟

API RESTful em Java com **Spring Boot 4** e **MySQL** para gestão completa de uma loja de Baozi (pãezinhos chineses recheados ao vapor).

---

## 🛠️ Tecnologias Utilizadas

- **Java 21**
- **Spring Boot 4.1** (WebMvc, Data JPA, Security, Validation)
- **MySQL 8+** (Driver `mysql-connector-j`)
- **H2 Database** (utilizado no perfil de testes automatizados)
- **Maven** (Gerenciador de dependências e build)
- **JUnit 5 & Mockito** (Testes unitários e de integração)

---

## 🏗️ Arquitetura do Projeto

O projeto adota uma arquitetura em camadas limpa e desacoplada:

```
src/main/java/br/com/joel/baozistore/
├── config/             # Configurações de segurança (SecurityConfig)
├── controller/         # Endpoints REST (Cliente, Produto, Pedido)
├── dto/                # Records DTOs para Request e Response
├── exception/          # Tratamento global de erros (@RestControllerAdvice)
├── model/              # Entidades JPA (Cliente, Produto, Pedido, ItemPedido, Enums)
├── repository/         # Interfaces Spring Data JPA
├── service/            # Regras de negócio, transações e validações
└── BaozistoreApplication.java
```

---

## 🗄️ Integração com o Banco de Dados MySQL

### 1. Pré-requisito
Certifique-se de que o servidor MySQL está em execução na porta `3306`. Caso utilize Docker:

```bash
docker run -d --name mysql-baozistore -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=baozistore_db mysql:8.0
```

### 2. Configurações (`application.properties`)
O projeto utiliza a flag `createDatabaseIfNotExist=true`, portanto o banco `baozistore_db` e as tabelas (`clientes`, `produtos`, `pedidos`, `itens_pedido`) serão criados e atualizados automaticamente pelo Hibernate:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/baozistore_db?createDatabaseIfNotExist=true&useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=root
spring.jpa.hibernate.ddl-auto=update
```

Você também pode sobrescrever via variáveis de ambiente:
- `DB_URL`
- `DB_USER`
- `DB_PASSWORD`
- `MYSQL_HOST`, `MYSQL_PORT`, `MYSQL_DB`

---

## 🚀 Como Executar

### Executar a Aplicação:
```bash
./mvnw spring-boot:run
```
A API iniciará por padrão em `http://localhost:8080`.

### Executar os Testes Automatizados:
```bash
./mvnw test
```
*Os testes utilizam H2 em memória de forma isolada, não necessitando de MySQL ativo durante o build.*

---

## 📡 Endpoints da API

### 👤 Clientes (`/api/clientes`)
- `GET /api/clientes` - Listar todos os clientes
- `GET /api/clientes/{id}` - Buscar cliente por ID
- `POST /api/clientes` - Cadastrar novo cliente
- `PUT /api/clientes/{id}` - Atualizar dados do cliente
- `DELETE /api/clientes/{id}` - Remover cliente

### 🥟 Produtos (`/api/produtos`)
- `GET /api/produtos` - Listar todos os produtos
- `GET /api/produtos/ativos` - Listar apenas produtos ativos para venda
- `GET /api/produtos/categoria/{categoria}` - Filtrar por categoria (`BAOZI_SALGADO`, `BAOZI_DOCE`, `BEBIDA`, `ACOMPANHAMENTO`)
- `GET /api/produtos/{id}` - Buscar produto por ID
- `POST /api/produtos` - Cadastrar produto com quantidade em estoque
- `PUT /api/produtos/{id}` - Atualizar produto
- `DELETE /api/produtos/{id}` - Remover produto

### 🛍️ Pedidos (`/api/pedidos`)
- `POST /api/pedidos` - Criar novo pedido (valida e debita estoque automaticamente)
- `GET /api/pedidos` - Listar todos os pedidos
- `GET /api/pedidos/{id}` - Detalhes do pedido com itens e total
- `GET /api/pedidos/cliente/{clienteId}` - Listar pedidos de um cliente
- `PATCH /api/pedidos/{id}/status` - Atualizar status (`CONFIRMADO`, `EM_PREPARO`, `PRONTO`, `ENTREGUE`, `CANCELADO`)
- `POST /api/pedidos/{id}/cancelar` - Cancela o pedido e **devolve os itens ao estoque**
- `DELETE /api/pedidos/{id}` - Excluir pedido do sistema (restaura itens ao estoque)
