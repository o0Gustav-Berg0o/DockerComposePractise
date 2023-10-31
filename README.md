//Compose file


version: '3'
services:
  mssql-server:
    image: mcr.microsoft.com/mssql/server:2017-latest-ubuntu
    environment:
      ACCEPT_EULA: "Y"
      SA_PASSWORD: "Docker2021"
      MSSQL_PID: Express
    ports:
    - "1433:1433"
  book-app:
    build: .
    environment:
        DatabaseServer: "mssql-server"
        DatabasePort: "1433"
        DatabaseUser: "SA"
        DatabasePassword: "Docker2021"
        DatabaseName: "Booksdb"
    ports:
    - "8090:80"



//Dockerfile


FROM mcr.microsoft.com/dotnet/sdk:5.0 AS build-env
WORKDIR /app

COPY *.csproj ./
RUN dotnet restore

COPY . ./
RUN dotnet publish -c Release -o out

FROM mcr.microsoft.com/dotnet/sdk:5.0
WORKDIR /app
EXPOSE 80
COPY --from=build-env /app/out .
ENTRYPOINT ["dotnet", "book.dll"]
