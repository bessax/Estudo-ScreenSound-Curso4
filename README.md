# 🎵 ScreenSound
 
Projeto de estudo (Curso 4) construído com **.NET 8** que reúne, em uma única solução, um **catálogo de artistas, músicas e gêneros musicais** com:
 
- uma **API Minimal API** com autenticação via **ASP.NET Core Identity**;
- um **front-end Blazor WebAssembly** com **MudBlazor**;
- um **aplicativo de console** (versão original do ScreenSound);
- projetos compartilhados de **modelos** e **acesso a dados** com **Entity Framework Core + SQL Server**.
---
 
## 🏗️ Arquitetura da solução
 
```
ScreenSound.sln
├── ScreenSound.Shared.Modelos   # Entidades de domínio: Artista, Musica, Genero
├── ScreenSound.Shared.Dados     # DbContext (Identity), DAL<T> genérico, Migrations
├── ScreenSound.API              # Minimal API + Swagger + Identity (endpoints /auth)
├── ScreenSound.Web              # Front-end Blazor WebAssembly (MudBlazor)
└── ScreenSound                  # Aplicativo de console (menu interativo)
```
 
```
 Blazor WASM (Web) ──HTTP/JSON──▶ API ──▶ DAL<T> ──▶ EF Core ──▶ SQL Server (LocalDB)
                                   │
                                   └── ASP.NET Core Identity (/auth)
 
 Console (ScreenSound) ─────────▶ DAL<T> ──▶ EF Core ──▶ SQL Server (LocalDB)
```
 
## ✨ Funcionalidades
 
**API**
- CRUD de **Artistas** (com upload de foto de perfil em Base64, salva em `wwwroot/FotosPerfil`).
- CRUD de **Músicas**, com ano de lançamento, vínculo ao artista e relação **muitos-para-muitos** com gêneros (gêneros já existentes são reaproveitados pelo nome).
- Cadastro, listagem, busca e exclusão de **Gêneros**.
- **Autenticação e autorização** com Identity API Endpoints: todos os grupos (`/artistas`, `/musicas`, `/genero`) exigem usuário autenticado.
- Documentação interativa com **Swagger**.
- Política de **CORS** configurada para o front-end.
**Web (Blazor WebAssembly)**
- Listagem de artistas em cards, cadastro e edição de artista.
- Listagem de músicas em cards.
- Layout com menu lateral usando **MudBlazor**.
- Serviços tipados (`ArtistaAPI`, `MusicaAPI`) consumindo a API via `IHttpClientFactory`.
**Console**
- Menu interativo para registrar artistas e músicas e consultar artistas e músicas cadastrados.
## 🧰 Tecnologias
 
| Camada | Tecnologias |
|---|---|
| Linguagem / Plataforma | C#, .NET 8 |
| API | ASP.NET Core Minimal APIs, Swashbuckle (Swagger), Identity API Endpoints |
| Dados | Entity Framework Core 7 (SQL Server), Lazy Loading Proxies, Migrations |
| Front-end | Blazor WebAssembly, MudBlazor 6.11.1 |
| Banco de dados | SQL Server LocalDB (padrão) |
 
## ✅ Pré-requisitos
 
- [.NET SDK 8.0](https://dotnet.microsoft.com/download/dotnet/8.0)
- SQL Server ou **SQL Server LocalDB** (instalado junto com o Visual Studio, no Windows)
- Ferramenta do EF Core:
```bash
dotnet tool install --global dotnet-ef
```
- Recomendado: Visual Studio 2022 (ou VS Code / Rider)
## 🚀 Como executar
 
### 1. Clone o repositório
 
```bash
git clone https://github.com/<seu-usuario>/Estudo-ScreenSound-Curso4.git
cd Estudo-ScreenSound-Curso4
```
 
### 2. Configure a conexão com o banco
 
A string de conexão da API fica em `ScreenSound.API/appsettings.json`:
 
```json
"ConnectionStrings": {
  "ScreenSoundDB": "Data Source=(localdb)\\MSSQLLocalDB;Initial Catalog=ScreenSoundV0;Integrated Security=True;Encrypt=False;..."
}
```
 
Por padrão usa o **LocalDB**. Se usar outra instância do SQL Server, altere esse valor.
 
> ℹ️ O projeto de console utiliza a string de conexão definida diretamente em `ScreenSoundContext.cs`. Se mudar o banco, ajuste nos dois lugares.
 
### 3. Crie o banco de dados (Migrations)
 
```bash
dotnet ef database update --project ScreenSound.Shared.Dados --startup-project ScreenSound.API
```
 
Isso cria as tabelas de Artistas, Músicas, Gêneros e as tabelas do Identity.
 
### 4. Execute a API
 
```bash
dotnet run --project ScreenSound.API --launch-profile https
```
 
- API: `https://localhost:7089`
- Swagger: `https://localhost:7089/swagger`
### 5. Execute o front-end
 
Em outro terminal:
 
```bash
dotnet run --project ScreenSound.Web --launch-profile https
```
 
- Web: `https://localhost:7015`
A URL da API usada pelo front-end está em `ScreenSound.Web/wwwroot/appsettings.json` (`APIServer:Url`).
 
### 6. (Opcional) Execute o aplicativo de console
 
```bash
dotnet run --project ScreenSound
```
 
## 🔐 Autenticação
 
Os endpoints de dados exigem autenticação. Para testar pelo Swagger:
 
1. Crie um usuário em `POST /auth/register` (`email` e `password`).
2. Faça login em `POST /auth/login` e copie o `accessToken` retornado.
3. Clique em **Authorize** no Swagger e informe `Bearer <accessToken>`.
## 📡 Endpoints principais
 
| Método | Rota | Descrição |
|---|---|---|
| `GET` | `/artistas` | Lista todos os artistas |
| `GET` | `/artistas/{nome}` | Busca artista pelo nome |
| `POST` | `/artistas` | Cadastra artista (com foto em Base64) |
| `PUT` | `/artistas` | Atualiza nome e bio |
| `DELETE` | `/artistas/{id}` | Remove artista |
| `GET` | `/musicas` | Lista todas as músicas |
| `GET` | `/musicas/{nome}` | Busca música pelo nome |
| `POST` | `/musicas` | Cadastra música (com gêneros) |
| `PUT` | `/musicas` | Atualiza nome e ano de lançamento |
| `DELETE` | `/musicas/{id}` | Remove música |
| `GET` | `/genero` | Lista gêneros |
| `GET` | `/genero/{nome}` | Busca gênero pelo nome |
| `POST` | `/genero` | Cadastra gênero |
| `DELETE` | `/genero/{id}` | Remove gênero |
| `POST` | `/auth/register`, `/auth/login` | Cadastro e login (Identity) |
 
## 🗺️ Status e próximos passos
 
Este é um projeto de estudo, ainda em evolução:
 
- [ ] Implementar login/registro no front-end Blazor (a API já exige autenticação).
- [ ] Concluir as páginas *Cadastrar música* e *Músicas por gênero* (hoje apenas esqueletos).
- [ ] Filtrar a página *Músicas por artista* (hoje lista todas as músicas).
- [ ] Criar a página inicial (`Home`) real do ScreenSound.
- [ ] Alinhar as opções do menu do console (a opção 5 é exibida, mas ainda não está registrada).
- [ ] Mover a string de conexão do `ScreenSoundContext` para configuração.
- [ ] Adicionar testes automatizados.
## 🤝 Contribuindo
 
Sugestões e *pull requests* são bem-vindos! Abra uma *issue* para conversarmos sobre melhorias.
 
## 📄 Licença
 
Defina aqui a licença do projeto (por exemplo, [MIT](https://opensource.org/licenses/MIT)).
 
---
 
Feito com 💙 durante os estudos de **.NET, Blazor e Entity Framework Core**.
 
