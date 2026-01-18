# Products API

REST API for a product catalog built with .NET 6 and Entity Framework Core.

## Scope

**Goals:**
- CRUD operations for Categories and Products
- One-to-many relationship handling (Category → Products)
- Basic unit testing setup

**Non-goals:**
- Authentication/authorization
- Input validation beyond null checks
- Pagination, filtering, or sorting
- Error handling middleware
- Logging or observability

## Architecture

```
ApiCatalogo/
├── Controllers/     → HTTP endpoints (CategoriasController, ProdutosController)
├── Models/          → Entity classes (Categoria, Produto)
├── Context/         → EF Core DbContext with Fluent API configuration
└── Migrations/      → Database schema versioning

ApiCatalogoTests/    → xUnit tests against real MySQL instance
```

**Data flow:** Controller → DbContext → MySQL

No service layer or repository pattern. Controllers interact directly with EF Core.

## Technical Concepts Demonstrated

- EF Core Fluent API for schema configuration
- Navigation properties and foreign key relationships
- `AsNoTracking()` for read-only queries
- Route constraints (`{id:int:min(1)}`)
- Named routes with `CreatedAtRouteResult` for POST responses
- xUnit with FluentAssertions

## Trade-offs and Limitations

| Decision | Rationale |
|----------|-----------|
| No repository pattern | Simplicity; DbContext is already a unit of work |
| Tests hit real database | Avoids in-memory provider behavior differences |
| Sync methods (except one async GET) | Learning project; async added incrementally |
| No DTOs | Direct entity exposure; acceptable for scope |
| Hardcoded connection strings | Local development only |

## Reading the Code

**Entry points:**
- [Program.cs](ApiCatalogo/Program.cs) — Minimal hosting setup
- [AppDbContext.cs](ApiCatalogo/Context/AppDbContext.cs) — Schema configuration

**Controllers:**
- [CategoriasController.cs](ApiCatalogo/Controllers/CategoriasController.cs) — Includes eager loading example (`Include()`)
- [ProdutosController.cs](ApiCatalogo/Controllers/ProdutosController.cs) — Async GET example

**Tests:**
- [CategoriasUnitTestController.cs](ApiCatalogoTests/CategoriasUnitTestController.cs) — AAA pattern, real DB connection

## Running Locally

```bash
# Requires MySQL on localhost:3306
dotnet build
dotnet run --project ApiCatalogo
dotnet test ApiCatalogoTests
```

## Create tables

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


``` Uncomment line 31 and 32 of CategoriasUnitTestControler for populating. 

Swagger available at `/swagger` in Development mode.
