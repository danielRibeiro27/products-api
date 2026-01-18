# Copilot Instructions - ApiCatalogo

## Visão Geral da Arquitetura

API REST de catálogo de produtos em .NET 6 com Entity Framework Core e MySQL. Estrutura simples com dois domínios principais: **Categorias** e **Produtos** (relacionamento 1:N).

```
ApiCatalogo/           → Projeto principal da API
├── Models/            → Entidades anêmicas (Categoria, Produto)
├── Context/           → AppDbContext com Fluent API
├── Controllers/       → Controllers REST padrão
└── Migrations/        → Migrations EF Core

ApiCatalogoTests/      → Testes unitários com xUnit + FluentAssertions
```

## Comandos Essenciais

```bash
# Build e execução
dotnet build
dotnet run --project ApiCatalogo

# Testes unitários
dotnet test ApiCatalogoTests

# Migrations (executar na pasta ApiCatalogo)
dotnet ef migrations add NomeMigracao
dotnet ef database update
```

## Padrões e Convenções

### Controllers
- Rota base: `[Route("[controller]")]` (usa nome do controller)
- Nomenclatura de rotas nomeadas: `Name = "Obter{Entidade}"` (ex: `"ObterCategoria"`)
- Retorno de criação: `CreatedAtRouteResult` com rota nomeada
- Validação de null retorna `NotFound` com mensagem em português

```csharp
// Padrão de retorno POST - ver CategoriasController.cs
return new CreatedAtRouteResult("ObterCategoria", new { id = categoria.CategoriaId, categoria });
```

### Entity Framework
- DbContext com configuração via **Fluent API** em `OnModelCreating` (não Data Annotations)
- Tabelas mapeadas explicitamente: `[Table("NomeTabela")]` nos models
- Relacionamentos definidos em `AppDbContext.cs` linha 29
- Use `AsNoTracking()` para queries de leitura (performance)

### Models
- Classes anêmicas (apenas propriedades, sem lógica)
- Propriedades nullable (`string?`) para campos opcionais
- Inicialização de coleções no construtor (`Produtos = new List<Produto>()`)

## Banco de Dados

- **MySQL** via Pomelo.EntityFrameworkCore.MySql
- Connection string em `appsettings.json` → `ConnectionStrings:DefaultConnection`
- Porta padrão: 3306

## Testes

- Framework: **xUnit** com **FluentAssertions**
- Padrão AAA (Arrange-Act-Assert) nos testes
- `DBUnityTestsMockInitializer` para seed de dados de teste
- Testes conectam diretamente ao MySQL (não in-memory)

```csharp
// Padrão de teste - ver CategoriasUnitTestController.cs
[Fact]
public void GetCategorias_Return_OkResult()
{
    var controller = new CategoriasController(ctx);
    var data = controller.Get();
    Assert.IsType<List<Categoria>>(data.Value);
}
```

## Pontos de Atenção

- Código e mensagens de erro em **português brasileiro**
- Swagger habilitado apenas em Development (`Program.cs` linha 17-21)
- `ProdutosController.GetAsync()` é async; demais métodos são síncronos
- Constraint de rota em Produtos: `{id:int:min(1)}` para validar ID positivo
