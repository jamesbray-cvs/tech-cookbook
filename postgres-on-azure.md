# Postgres on Azure

## Configuration

Configure the database script locations in the `application-azure.yaml` file.

```yaml
spring:
  datasource:
    azure:
      passwordless-enabled: true
  sql:
    init:
      schema-locations: classpath:db/azure/V1__grant_app_user_permissions.sql
```

The `spring.sql.init.schema-locations` configuration fixes an issue encountered when attempting to run the application in a local environment with a local Postgres database.  The specified role does not exist in the local environment, therefore this SQL script should only be invoked on the AKS platform.

## Passwordless Setup

Configure the following environment variables:

```cmd
export AZ_RESOURCE_GROUP=<YOUR_RESOURCE_GROUP>
export AZ_DATABASE_SERVER_NAME=<YOUR_DATABASE_SERVER_NAME>
export AZ_DATABASE_NAME=demo
export AZ_POSTGRESQL_AD_NON_ADMIN_USERNAME=<YOUR_AZURE_AD_NON_ADMIN_USER_DISPLAY_NAME>
export AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
export CURRENT_USERNAME=$(az ad signed-in-user show --query userPrincipalName --output tsv)
```

**Important Items!**

- Always set a schema in the configuration.  If a schema is not set, it will be set to "PUBLIC" by default, which cannot be written to. `?currentSchema=myschema`

## Resources

- [Migrate an application to use passwordless connections with Azure Database for PostgreSQL](https://learn.microsoft.com/en-us/azure/developer/java/spring-framework/migrate-postgresql-to-passwordless-connection?tabs=sign-in-azure-cli%2Cspring%2Capp-service%2Cassign-role-service-connector)