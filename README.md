# Core API Auth Using JWT

An **ASP.NET Core Web API (.NET 9)** starter for **JWT-based authentication and role-based authorization** using **ASP.NET Core Identity** and **Entity Framework Core** on SQL Server. The solution is split into an API project and a shared class library for DTOs and models, so a future client (Blazor, MVC or mobile) can reuse them.

> **Status: work in progress.** The Identity setup, JWT validation pipeline and the account / login logic are in place. The HTTP endpoints that expose them are the next step (see [Roadmap](#roadmap)).

## What's Implemented

- **ASP.NET Core Identity** with a custom `ApplicationUser` (adds `Name`), stored through EF Core
- **JWT Bearer authentication** configured in `Program.cs` (issuer, audience, signing key and lifetime validation)
- **User repository** (`IUserRepository` / `UserRepository`)
  - `CreateAccount` – duplicate email check, user creation, automatic role creation (the first account becomes `Admin`, later accounts get `User`)
  - `LoginResponse` – password check, role lookup and JWT generation with `NameIdentifier`, `Name`, `Email` and `Role` claims
- **Swagger UI** with an `Authorization` header input for testing bearer tokens (Development only)
- **CORS policy** (`EnableCORS`)
- **Shared library** with `LoginDTO`, `UserDTO`, `ServiceResponse` records and the `Employee` / `Experience` models
- **EF Core migrations** for the Identity tables and the employee tables

## Tech Stack

| Layer | Technology |
| --- | --- |
| Framework | ASP.NET Core Web API, .NET 9 |
| Language | C# |
| Auth | ASP.NET Core Identity, JWT Bearer (`Microsoft.AspNetCore.Authentication.JwtBearer`) |
| ORM | Entity Framework Core 9 (Code First) |
| Database | SQL Server LocalDB |
| API docs | Swashbuckle (Swagger UI) |

## Solution Structure

```
CoreApiAuthUsingJWT/
├── CoreApiAuthUsingJWT.sln
├── CoreAuthApi/                 # Web API project
│   ├── Controllers/             # WeatherForecastController (template sample)
│   ├── Data/                    # ApplicationUser, IdentityAuthDBContext
│   ├── Repository/              # IUserRepository, UserRepository (register, login, token)
│   ├── Migrations/              # EF Core migrations
│   ├── Program.cs               # Identity, JWT, Swagger, CORS setup
│   └── appsettings.json
└── SharedLibrary/               # Class library shared with clients
    ├── DTOs/                    # LoginDTO, UserDTO, ServiceResponse
    └── Models/                  # Employee, Experience
```

## Getting Started

### Prerequisites

- [.NET 9 SDK](https://dotnet.microsoft.com/download/dotnet/9.0)
- SQL Server LocalDB (installed with Visual Studio) or any SQL Server instance

### Run locally

1. Clone the repository:
   ```bash
   git clone https://github.com/<your-username>/<your-repo>.git
   cd <your-repo>/CoreAuthApi
   ```
2. Set the connection string and JWT settings in `appsettings.json`:
   ```json
   {
     "ConnectionStrings": {
       "con": "server=(LocalDB)\\MSSQLLocalDB; database=JWT70DB; Trusted_Connection=true; MultipleActiveResultSets=true"
     },
     "Jwt": {
       "Issuer": "http://localhost:5147",
       "Audience": "http://localhost:5147"
     }
   }
   ```
3. Store the signing key with **user secrets** instead of committing it. Use a random value of at least 32 characters, because HMAC-SHA256 needs a key of 128 bits or more:
   ```bash
   dotnet user-secrets init
   dotnet user-secrets set "Jwt:Key" "<your-long-random-secret-key>"
   ```
4. Create the database:
   ```bash
   dotnet tool install --global dotnet-ef   # once
   dotnet ef database update
   ```
5. Run the API:
   ```bash
   dotnet run
   ```
6. Open Swagger at **http://localhost:5147/swagger**.

## Roadmap

- [ ] `AuthController` with `POST /api/auth/register` and `POST /api/auth/login`
- [ ] Register `IUserRepository` in dependency injection
- [ ] Protect endpoints with `[Authorize]` and `[Authorize(Roles = "Admin")]`
- [ ] Employee CRUD endpoints (the `Employee` and `Experience` tables already exist)
- [ ] Refresh tokens and token expiry in UTC
- [ ] Restrict CORS to known origins
- [ ] Unit tests for the user repository

## License

Add a license of your choice (for example MIT), or remove this section.
