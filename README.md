# bank-config-repository

Repositorio Git de configuracion no sensible para los cuatro microservicios bancarios.

## Topologia local

| Aplicacion | HTTP | PostgreSQL |
|---|---:|---|
| bank-ms-customer | 8080 | localhost:5432/bank_customer_dev |
| bank-ms-account | 8081 | localhost:5433/bank_account_dev |
| bank-ms-card | 8082 | localhost:5435/bank_card_dev |
| bank-ms-credit | 8083 | localhost:5434/bank_credit_dev |
| bank-config-server | 8888 | No aplica |

## Variables de entorno requeridas

Configurar en cada Run Configuration de IntelliJ IDEA:

```text
CUSTOMER_DB_USERNAME=bank_customer_user
CUSTOMER_DB_PASSWORD=bank_customer_password
ACCOUNT_DB_USERNAME=bank_account_user
ACCOUNT_DB_PASSWORD=bank_account_password
CARD_DB_USERNAME=bank_card_user
CARD_DB_PASSWORD=bank_card_password
CREDIT_DB_USERNAME=bank_credit_user
CREDIT_DB_PASSWORD=bank_credit_password
CONFIG_SERVER_ENABLED=true
SPRING_PROFILES_ACTIVE=local
```

Las credenciales anteriores son solo para desarrollo local y se corresponden con `docker-compose-databases.yml`. No deben utilizarse en ambientes compartidos o productivos. HashiCorp Vault las reemplazara posteriormente.

## Inicializacion Git en Windows

```powershell
cd "C:\Dev\Springboot\Proyecto Bancaank-config-repository"
git init
git branch -M main
git add .
git commit -m "Initial centralized configuration"
```

## Endpoints de verificacion

```text
http://localhost:8888/bank-ms-customer/local
http://localhost:8888/bank-ms-account/local
http://localhost:8888/bank-ms-card/local
http://localhost:8888/bank-ms-credit/local
```

## Configuracion local minima que permanece en cada microservicio

```properties
spring.application.name=<nombre-exacto-del-microservicio>
spring.profiles.active=${SPRING_PROFILES_ACTIVE:local}
spring.config.import=optional:configserver:${CONFIG_SERVER_URL:http://localhost:8888}
spring.cloud.config.enabled=${CONFIG_SERVER_ENABLED:true}
```

## Observaciones

- `bank-ms-customer-local.yml` conserva `separator: "@@"`. Eliminarlo si `schema.sql` usa `;` como separador.
- La comision de Account esta deshabilitada en Fase I mediante `enabled: false`.
- Se conservan las claves actuales del cliente Customer para no mezclar esta migracion con un refactor.
- No versionar usuarios, contrasenas, tokens, role-id, secret-id, claves privadas o certificados.
- Los cambios deben confirmarse en Git antes de ser leidos por Config Server.
