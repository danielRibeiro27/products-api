# Products API (ApiCatalogo)

**A RESTful API for managing product catalogs with categories**

## Overview

This is a production-ready ASP.NET Core Web API for managing a product catalog system. The API enables full CRUD operations for products and categories, demonstrating best practices in .NET development, database design with Entity Framework Core, and RESTful API architecture. Built with MySQL as the data store, the application showcases clean separation of concerns, async/await patterns for optimal performance, and comprehensive error handling.

## Tech Stack

**Backend & Framework:**
- .NET 6 (ASP.NET Core Web API)
- C# 10

**Database & ORM:**
- MySQL 8.0+
- Entity Framework Core 7.0.2
- Pomelo.EntityFrameworkCore.MySql 7.0.0 (MySQL provider)

**API Documentation:**
- Swagger/OpenAPI (Swashbuckle.AspNetCore 6.5.0)

**Testing:**
- xUnit
- Entity Framework In-Memory Database for unit tests

**Development Tools:**
- Visual Studio / Visual Studio Code
- Entity Framework Migrations for schema management

## Skills & Responsibilities Demonstrated

This project showcases proficiency in:

### API Design & Development
- RESTful API design with proper HTTP verbs (GET, POST, PUT, DELETE)
- Route configuration and attribute routing
- Request/response handling with ActionResult patterns
- Async/await for non-blocking I/O operations
- Content negotiation and serialization

### Database Design & Management
- Relational database modeling (one-to-many relationships)
- Entity Framework Core Code-First approach
- Database migrations for version control of schema
- DbContext configuration and entity mapping
- Query optimization with AsNoTracking() for read operations
- Foreign key constraints and cascade delete behavior

### Software Architecture
- Layered architecture (Controllers, Models, Data Context)
- Dependency injection for DbContext
- Separation of concerns
- Repository pattern implementation via EF Core

### Error Handling & Validation
- HTTP status code usage (200, 201, 400, 404, 500)
- Null reference checking
- Exception handling with try-catch blocks
- Input validation

### Testing Strategy
- Unit testing with xUnit framework
- Test data initialization with mock seeders
- Arrange-Act-Assert (AAA) pattern
- Integration testing with test database

### Developer Practices
- Version control with Git
- Code comments and documentation
- Consistent naming conventions
- SOLID principles application

## Getting Started

### Prerequisites

- [.NET 6 SDK](https://dotnet.microsoft.com/download/dotnet/6.0) or higher
- [MySQL Server 8.0+](https://dev.mysql.com/downloads/mysql/)
- Visual Studio 2022 / Visual Studio Code / Rider (optional, for development)
- Git (for cloning the repository)

### Installation & Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/danielRibeiro27/products-api.git
   cd products-api
   ```

2. **Configure the database connection**
   
   Update the connection string in `ApiCatalogo/appsettings.json`:
   ```json
   {
     "ConnectionStrings": {
       "DefaultConnection": "Server=localhost;Port=3306;Database=CatalogoMinApi;Uid=yourUsername;Pwd=yourPassword;"
     }
   }
   ```

3. **Create the database tables**
   
   Run the SQL scripts provided in the [Database Schema](#database-schema) section below to create the required tables, **OR** use Entity Framework migrations:
   
   ```bash
   cd ApiCatalogo
   dotnet ef database update
   ```

4. **Restore dependencies**
   ```bash
   dotnet restore
   ```

5. **Run the application**
   ```bash
   cd ApiCatalogo
   dotnet run
   ```

6. **Access the API**
   - API Base URL: `https://localhost:7xxx` or `http://localhost:5xxx` (check console output)
   - Swagger UI: `https://localhost:7xxx/swagger` (available in Development mode)

### Running Tests

```bash
cd ApiCatalogoTests
dotnet test
```

**Note:** Before running tests, ensure the test database connection string is configured in `CategoriasUnitTestController.cs` (line 16).

## API Endpoints

### Categories (Categorias)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/Categorias` | Get all categories |
| GET | `/Categorias/{id}` | Get category by ID |
| GET | `/Categorias/produtos` | Get categories with their products (first 5) |
| POST | `/Categorias` | Create a new category |
| PUT | `/Categorias/{id}` | Update an existing category |
| DELETE | `/Categorias/{id}` | Delete a category |

### Products (Produtos)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/Produtos` | Get all products |
| GET | `/Produtos/{id}` | Get product by ID |
| POST | `/Produtos` | Create a new product |
| PUT | `/Produtos/{id}` | Update an existing product |
| DELETE | `/Produtos/{id}` | Delete a product |

**Sample Request Body (Create Product):**
```json
{
  "nome": "Coca-Cola 2L",
  "descricao": "Refrigerante de cola",
  "imagem": "coca-cola.jpg",
  "preco": 8.99,
  "estoque": 100,
  "dataCadastro": "2024-01-18T00:00:00",
  "categoriaId": 1
}
```

## Database Schema

### Create Tables

Run the following SQL scripts to create the database tables:

```sql
CREATE TABLE `categorias` (
  `CategoriaId` int NOT NULL AUTO_INCREMENT,
  `Nome` varchar(80) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
  `ImageUrl` varchar(300) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
  PRIMARY KEY (`CategoriaId`)
) ENGINE=InnoDB AUTO_INCREMENT=19 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

CREATE TABLE produtos (
  `ProdutoId` int NOT NULL AUTO_INCREMENT,
  `Nome` varchar(80) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
  `Descricao` varchar(300) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
  `ImagemUrl` varchar(300) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL,
  `Estoque` float NOT NULL,
  `DataCadastro` datetime(6) NOT NULL,
  `CategoriaId` int NOT NULL,
  `Preco` decimal(10,2) NOT NULL DEFAULT '0.00',
  PRIMARY KEY (`ProdutoId`),
  KEY `IX_Produtos_CategoriaId` (`CategoriaId`),
  CONSTRAINT `FK_Produtos_Categorias_CategoriaId` FOREIGN KEY (`CategoriaId`) REFERENCES `categorias` (`CategoriaId`) ON DELETE CASCADE
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
```

**Note:** To populate the categories table with sample data, uncomment lines 31 and 32 in `CategoriasUnitTestController.cs`.

### Entity Relationship

```
Categoria (1) ─────< (N) Produto
```

- One **Category** can have many **Products**
- Each **Product** belongs to exactly one **Category**
- Cascade delete: removing a category deletes all its products

## Project Structure

```
products-api/
├── ApiCatalogo/                    # Main API project
│   ├── Controllers/                # API Controllers (REST endpoints)
│   │   ├── CategoriasController.cs
│   │   └── ProdutosController.cs
│   ├── Models/                     # Entity models
│   │   ├── Categoria.cs
│   │   └── Produto.cs
│   ├── Context/                    # Database context
│   │   └── AppDbContext.cs
│   ├── Migrations/                 # EF Core migrations
│   ├── Program.cs                  # Application entry point
│   └── appsettings.json            # Configuration
├── ApiCatalogoTests/               # Unit tests project
│   ├── CategoriasUnitTestController.cs
│   └── DBUnityTestsMockInitializer.cs
└── ApiCatalogo.sln                 # Solution file
```

## Future Improvements

- [ ] Add authentication and authorization (JWT tokens)
- [ ] Implement pagination for GET all endpoints
- [ ] Add filtering and search capabilities
- [ ] Implement PATCH endpoints for partial updates
- [ ] Add data validation with Data Annotations or FluentValidation
- [ ] Implement logging (e.g., Serilog)
- [ ] Add caching layer (Redis or in-memory)
- [ ] Create Docker containerization
- [ ] Set up CI/CD pipeline
- [ ] Add API versioning
- [ ] Implement rate limiting
- [ ] Add comprehensive integration tests
- [ ] Create API client SDK

## License

This project is available for educational and portfolio purposes.

## Author

**Daniel Ribeiro**  
[GitHub: @danielRibeiro27](https://github.com/danielRibeiro27)

---

*This API was developed as a demonstration of ASP.NET Core Web API development skills, showcasing enterprise-level patterns and best practices in modern .NET development.*
