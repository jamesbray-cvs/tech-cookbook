# Passwordless Database Access

Configure the `application.yaml` file to include a property for passwordless authentication via Azure.

```yaml
spring:
  datasource:
    azure:
      passwordless-enabled: true
```

## Flyway Configuration


## Testing

## Notes

Splunk Search:  `index="cloudeng_aks" cluster_name="aks-corp-util-dev-usc" container_name="deveco-gradle-postgres"`

## Resources

- [Migrate an application to use passwordless connections with Azure Database for PostgreSQL](https://learn.microsoft.com/en-us/azure/developer/java/spring-framework/migrate-postgresql-to-passwordless-connection?tabs=sign-in-azure-cli%2Cspring%2Capp-service%2Cassign-role-service-connector)
- [Express Cloud Resource Guide](https://docs.azure.cvshealth.com/category/resource-guides)



[11:34 AM] Gryska, Evan
Connection String - jdbc:postgresql://psql-flex-corp-util-dev-usc-deveco-gradle-postgres-store.postgres.database.azure.com:5432/deveco_gradle_dev_pgs_db?sslmode=require&currentSchema=deveco_gradle_dev_pgs_db_schema
DDL (Flyway) User
Managed Identity = mi-corp-util-dev-usc-deveco-gradle-postgres-flyway
client ID = be9c94ab-b712-4a9b-94d7-8abe6ba6ff3a
database username = deveco-gradle-postgres-flyway_deveco_gradle_dev_pgs_db
DML (Spring Datasource) User
Managed Identity = mi-corp-util-dev-usc-deveco-gradle-postgres
client ID = bd384ff0-e12a-4538-91a4-0748cc46af3c
database username = deveco-gradle-postgres_deveco_gradle_dev_pgs_db
