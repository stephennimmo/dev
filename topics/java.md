# Java
- Use the latest LTS release for all new code
- Maven is the preferred build tool

## Quarkus
- Validation messages are stored in 'src/main/resources/ValidationMessages.properties'
- Records should be used as DTOs everywhere possible.
- Optional should be used for any nullable return types
- Hibernate Validator @Valid annotations should be used on all appropriate service layer and repository layers.
- Configuration should be in YAML and follow the "%dev", "%test", "%prod" structure.
- All OIDC integration should be done agnostic to the OIDC provider. It should allow for complete flexibility in which OIDC provider to use.
- Banner turned off
- Create new quarkus projects using the latest version and cli like this
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

### Quarkus - REST API
- Use openapi specification for all REST endpoints
- REST API development should follow the three tier structure: repository, service, api. Example packages: com.examplecompany.${project_name}.{api|service|repository}
    - The api package should contain all Resource classes as well as the Request and Response records used to define the API. The Resource classes should primarily interact with the service layer.
    - the service package should contain the Service classes and all the Domain records. All public methods on Service classes should produce and consume Domain records. The service layer should only interact with the repository layer.
    - The repository package should contain the Repository classes as well as the Entity classes. All public methods on Repository classes should produce and consume Entity classes.
- All data access should be performed using the repository layer and should only be accessed through a service layer.
- The service layer should consume and return only domain objects.
- The `@RolesAllowed` annotations should always be at the method level
- Put roles into an enum called Roles and use that in the @RolesAllowed instead of strings.
- Resource class methods for API endpoints should always return `jakarta.ws.rs.core.Response` objects with the correct HTTP status code.
- Any generated Ids should follow the name entityId, not just id. ex, Person has personId, Bill has billId, Premise has premiseId.

### Quarkus - Panache Framework
- The JPA Entity classes should follow the rules:
  - Suffix should be 'Entity'. ex. PersonEntity, CarEntity
  - Class annotations should always include the @Entity(name="Entity") and @Table(name="entity") to specify the name of the entity and the exact table.
  - Id should follow the pattern of TypeId, not just id. For example, for a Person, the entity would be PersonEntity and the id would be personId and the database column should be person_id
  - Should always include @Column with name attribute specified and any nullable information based on the schema
  - jakarta validations should be used to validate the individual fields and should reference validation messages
  - Fields should be public
  - HashCode, equals and toString should be generated and present
- Repository pattern should be used

### Quarkus - Protobuf and gRPC
- Use proto3
- Enum definitions always have an `UNSPECIFIED` as the 0 value
- All values should be prefixed with the all caps snake case value of the name of the enum
- Example
```
enum ConnectionType {
  CONNECTION_TYPE_UNSPECIFIED = 0;
  CONNECTION_TYPE_INITIATOR = 1;
  CONNECTION_TYPE_ACCEPTOR = 2;
}
```

### Quarkus - Quinoa
- The only two additions needed for the Quinoa to run are the following:
    - `yq -i '.quarkus.quinoa.enable-spa-routing = true' src/main/resources/application.yml`
    - `yq -i '.quarkus.quinoa.build-dir = "dist/'$NG_PROJECT_NAME'/browser"' src/main/resources/application.yml`
