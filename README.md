# Servidor MCP Bancario (Spring AI + Claude Desktop)

Servidor MCP desarrollado con Spring Boot 3.5.3 y Spring AI 1.0.0 que permite a Claude Desktop
realizar operaciones bancarias en lenguaje natural sobre una base de datos PostgreSQL.

**Equipo:** Daniel Salgado Mazo, Juan David Castro, Duvan Pinilla
**Curso:** TdeA 2026

## Arquitectura

Claude Desktop (host MCP) → cliente MCP → transporte Stdio → servidor MCP (Spring Boot) → PostgreSQL

## Herramientas expuestas (`@Tool`)

| Herramienta | Descripción |
|---|---|
| `createAccount` | Crea una cuenta con titular y saldo inicial |
| `getBalance` | Consulta el saldo de una cuenta |
| `deposit` | Consignación en una cuenta |
| `transfer` | Transferencia entre cuentas (transaccional) |
| `requestCredit` | Crea una solicitud de crédito (estado PENDING) |
| `getTransactions` | Lista los movimientos de una cuenta |
| `getCreditRequests` | Lista las solicitudes de crédito de una cuenta |

## Base de datos

Tablas generadas automáticamente por Hibernate: `accounts`, `bank_transactions`, `credit_requests`.

## Requisitos

- Java 17 o superior
- Maven (o el wrapper `mvnw` incluido)
- PostgreSQL
- Claude Desktop

## Configuración y ejecución

1. Crear la base de datos en PostgreSQL:
   ```sql
   CREATE DATABASE banco_mcp;
   ```
2. Compilar el JAR:
   ```powershell
   .\mvnw clean package -DskipTests
   ```
3. Registrar el servidor en `claude_desktop_config.json`
   (`%APPDATA%\Claude\claude_desktop_config.json`), ajustando la ruta del JAR y la contraseña:
   ```json
   {
     "mcpServers": {
       "bank-mcp-server": {
         "command": "java",
         "args": ["-jar", "C:\\ruta\\al\\proyecto\\target\\mcp-server-tdea-0.0.1-SNAPSHOT.jar"],
         "env": { "DB_PASSWORD": "TU_CLAVE_DE_POSTGRES" }
       }
     }
   }
   ```
   Variables opcionales: `DB_URL` (por defecto `jdbc:postgresql://localhost:5432/banco_mcp`) y `DB_USER` (por defecto `postgres`).
4. Cerrar Claude Desktop por completo y abrirlo de nuevo. En Ajustes → Desarrollador, el servidor
   debe aparecer "En ejecución".

## Pruebas (MCP Inspector)

```powershell
npx @modelcontextprotocol/inspector -e DB_PASSWORD=TU_CLAVE java -jar "C:\ruta\al\proyecto\target\mcp-server-tdea-0.0.1-SNAPSHOT.jar"
```

## Ejemplos de prompts en Claude Desktop

- "Crea una cuenta para Juan Pérez con saldo inicial de 100000."
- "Consigna 20000 en la cuenta de Juan."
- "Transfiere 30000 de la cuenta de Juan a la de María."
- "Muéstrame los movimientos de la cuenta de Juan."
- "Solicita un crédito de 2000000 a 12 meses para la cuenta de María."
