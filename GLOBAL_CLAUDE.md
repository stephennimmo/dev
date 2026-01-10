# GLOBAL_CLAUDE.md

## Development

### Angular

- Use signals whereever possible
- Use inject() rather than constructor injection
- use ng-bootstrap components whereever possible - https://ng-bootstrap.github.io/#/components/accordion/overview
- Page components should be stored in the 'src/app/pages' folder and have a suffix of '.page.' and the classnames should end in Page.
- If there is a modal component that is specific to a single page, put it into that page's folder.
- Shared components, such as the navbar, should be stored in 'src/app/pages/shared' and have a suffix of '.component.' and the classnames should end in Component
- Guards should be stored in 'src/app/guards' and have a suffix of '.guard.ts' and the classnames should end in Guard
- Services should be stored in 'src/app/services' and have a suffix of '.service.ts' and the classnames should end in Service
- All OIDC integration should be done agnostic to the OIDC provider. It should allow for complete flexibility in which OIDC provider to use. 
- Create new angular projects like this
```shell
NG_PROJECT_NAME=crm-ng
ng new --routing --style scss --ssr false --zoneless false --ssr false --defaults $NG_PROJECT_NAME
cd "$NG_PROJECT_NAME"
ng add @ng-bootstrap/ng-bootstrap --skip-confirmation
ng generate environments
git add .
git commit -m 'Angular project init'
```

### Quarkus
- Flyway should be used for all database schema management.
- Validation messages are stored in 'src/main/resources/ValidationMessages.properties'
- Records should be used as DTOs everywhere possible.
- Optional should be used for any nullable return types
- Hibernate Validator @Valid annotations should be used on all appropriate service layer and repository layers.
- Configuration should be in YAML and follow the "%dev", "%test", "%prod" structure.
- All OIDC integration should be done agnostic to the OIDC provider. It should allow for complete flexibility in which OIDC provider to use. 
- Create new quarkus projects like this
```
GROUP_ID=com.deltacholabs
ARTIFACT_ID=crm
quarkus create app --wrapper --no-code \
    -x config-yaml,rest-jackson,smallrye-openapi,smallrye-health,micrometer-registry-prometheus \
    $GROUP_ID:$ARTIFACT_ID:0.0.1-SNAPSHOT
cd "$ARTIFACT_ID"
git init
git add .
git commit -m 'Quarkus project init'
```

#### REST API
- Use openapi specification for all REST endpoints
- REST API development should follow the three tier structure: repository, service, api
- All data access should be performed using the repository layer and should only be accessed through a service layer.
- The service layer should consume and return only domain objects.
- The `@RolesAllowed` annotations should always be at the method level

#### Panache Framework
- The JPA Entity classes should follow the rules:
  - Suffix should be 'Entity'
  - Class annotations should always include the `@Entity(name="Entity") and @Table(name="entity") to specify the name of the entity and the exact table. 
  - Should always include @Column with name attribute specified and any nullable information based on the schema
  - jakarta validations should be used to validate the individual fields and should reference validation messages
  - Fields should be public
  - HashCode, equals and toString should be generated and present
- Repository pattern should be used

#### Postgres Database Schema
- BIGINT should be used for generated primary key.
- Sequences should also be set to a number to fill all the digits - ALTER SEQUENCE customer_customer_id_seq RESTART 1000000000000000000;
- Test data should be generated using primary key values lower than the starting sequence to easily be able to delete them
- Use TEXT over VARCHAR
- Use `created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP` and `updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP` where it makes sense

### Documentation

- Documentation sites are created using material for mkdocs - https://squidfunk.github.io/mkdocs-material
- Documentation sites are hosted on github using github actions - https://squidfunk.github.io/mkdocs-material/publishing-your-site/#with-github-actions

#### Markdown
- When creating markdown tables, use spaces to make the tables readable in raw format
