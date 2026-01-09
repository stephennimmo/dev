# GLOBAL_CLAUDE.md

## Development

### Angular

- Use signals whereever possible
- Use inject() rather than constructor injection

- Page components should be stored in the 'src/app/pages' folder and have a suffix of '.page.' and the classnames should end in Page.
- Shared components, such as the navbar, should be stored in 'src/app/pages/shared' and have a suffix of '.component.' and the classnames should end in Component
- Guards should be stored in 'src/app/guards' and have a suffix of '.guard.ts' and the classnames should end in Guard
- Services should be stored in 'src/app/services' and have a suffix of '.service.ts' and the classnames should end in Service
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

### REST API
- Use openapi specification for all REST endpoints
- REST API development should follow the three tier structure: repository, service, api
- All data access should be performed using the repository layer and should only be accessed through a service layer.
- The service layer should consume and return only domain objects.

### Panache Framework
- The JPA Entity classes should follow the rules:
  - Suffix should be 'Entity'
  - Class annotations should always include the @Entity and @Table to specify the name of the entity. 
  - Should always include @Column with name attribute specified and any nullable information based on the schema
  - jakarta validations should be used to validate the individual fields and should reference validation messages
  - Fields should be public
  - HashCode, equals and toString should be generated and present
- Repository pattern should be used

### Documentation

- Documentation sites are created using material for mkdocs - https://squidfunk.github.io/mkdocs-material
- Documentation sites are hosted on github using github actions - https://squidfunk.github.io/mkdocs-material/publishing-your-site/#with-github-actions
- 
