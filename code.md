# AGENTS

## General

- Don't copy this into any projects or anywhere locally. Just read it into the current context window.
- If there is documentation or a README present, if changes are made, please update the documentation.
- Preferred cloud environment is AWS, us-east-2 region
- YAML files end in .yaml, not .yml
- Use Cloudflare for upstream DNS - 1.1.1.1
- Authentication and authorization should be OIDC using JWT tokens.
- Absolutely no sensitive information should be stored in any git repository
- When generating documentation, put the instructions and commands after a small summary. and then provide details below all the instructions. 

## Java

- Use the latest LTS release for all new code
- Maven is the preferred build tool
- Records should be used as DTOs everywhere possible.
- Optional should be used for any nullable return types
- Don't use `@Inject`, but instead always use constructor injection. Example: 
```
private final CompanyRepository companyRepository;
    
public CompanyService(CompanyRepository companyRepository) {
    this.companyRepository = companyRepository;
}
```

### Validation

- Validator annotations should be included on all DTOs and domain objects.
- Hibernate Validator @Valid annotations should be used on all appropriate service layer and repository layers method signatures.
- Validation messages are stored in 'src/main/resources/ValidationMessages.properties'

### Quarkus

#### General Guidance

- Configuration should be in YAML and follow the "%dev", "%test", "%prod" structure.
- All OIDC integration should be done agnostic to the OIDC provider. It should allow for complete flexibility in which OIDC provider to use.
- Turn off banner in quarkus configuration
- Always use dev services when running locally

#### REST API

- Use openapi specification for all REST endpoints
- REST API development should follow the three tier structure: repository, service, api. Example packages: com.examplecompany.${project_name}.{api|service|repository}
    - The api package should contain all Resource classes as well as the Request and Response records used to define the API. The Resource classes should primarily interact with the service layer. `Resource` suffix for the api classes and `Request` or `Response` for the value objects. 
    - The service package should contain the Service classes and all the Domain records. All public methods on Service classes should produce and consume Domain records. Service classes should be suffix `Service`. Domain objects should just be the noun name. No need for a `Domain` suffix.  
    - The repository package should contain the Repository classes as well as the Entity classes. All public methods on Repository classes should produce and consume Entity classes. Follow the Repository and Entity suffix pattern. 
- All data access should be performed using the repository layer and should only be accessed through a service layer. 
- The service layer should consume and return only domain objects. The service layer should only interact with the repository layer.
- The api layer should only interact with the service layer. It should never directly interact with the repository layer. 
- The `@RolesAllowed` annotations should always be at the method level
- Put roles into an enum called Roles and use that in the @RolesAllowed instead of strings.
- Resource class methods for API endpoints should:
  - Always return `jakarta.ws.rs.core.Response` objects with the correct HTTP status code.
  - Use `@APIResponse` to define the response codes and the return types, as well as defining the relevant error states, such as 404 for not found or 400 for bad request. 
- Any generated Ids should follow the name entityId, not just id. ex, Person has personId, Bill has billId, Premise has premiseId.
- Extensive tests should always be created to test the API endpoints for the project. This is not optional.
- Roles are done with interface. Example: 
```
public interface Roles {
    String CRM_READ = "crm-read";
    String CRM_WRITE = "crm-write";
    String CRM_ADMIN = "crm-admin"; 
}
```

#### Panache Framework

- The JPA Entity classes should follow the rules:
  - Suffix should be 'Entity'. ex. PersonEntity, CarEntity
  - Class annotations should always include the @Entity(name="Entity") and @Table(name="entity") to specify the name of the entity and the exact table.
  - Id should follow the pattern of TypeId, not just id. For example, for a Person, the entity would be PersonEntity and the id would be personId and the database column should be person_id
  - Should always include @Column with name attribute specified and any nullable information based on the schema
  - jakarta validations should be used to validate the individual fields and should reference validation messages
  - Fields should be public
  - HashCode, equals and toString should be generated and present
- Repository pattern should be used

#### Protobuf and gRPC

- Use proto3
- Enum definitions always have an `UNSPECIFIED` as the 0 value
- All values should be prefixed with the all caps snake case value of the name of the enum
- Example:
```
enum ConnectionType {
  CONNECTION_TYPE_UNSPECIFIED = 0;
  CONNECTION_TYPE_INITIATOR = 1;
  CONNECTION_TYPE_ACCEPTOR = 2;
}
```

#### Quinoa

- Always put Quinoa based web projects in `src/main/webui`
- The only two additions needed for the Quinoa to run are the following:
    - `yq -i '.quarkus.quinoa.enable-spa-routing = true' src/main/resources/application.yml`
    - `yq -i '.quarkus.quinoa.build-dir = "dist/'$NG_PROJECT_NAME'/browser"' src/main/resources/application.yml`

#### Flyway Database Schema

- Use Flyway for all schema management
    - Test Data for Flyway should be placed in a file named `src/main/resources/db/testdata/V999__testdata.sql`
    - dev and test profiles should include the `db/testdata` folder with the `locations: db/migration,db/testdata` but not prod
- Test data should be generated using primary key values lower than the starting sequence to easily be able to delete them

## Angular

- Create new angular projects like this:
```shell
NG_PROJECT_NAME=crm-ng
ng new --routing --style scss --ssr false --zoneless false --ssr false --defaults $NG_PROJECT_NAME
cd "$NG_PROJECT_NAME"
ng add @ng-bootstrap/ng-bootstrap --skip-confirmation
ng generate environments
git add .
git commit -m 'Angular project init'
```
- Use the latest LTS release for all development
- If the project is being created inside of an existing Quarkus project using Quinoa, then add `--skip-git` to the `ng new` command.
- Use ng-bootstrap components whereever possible - https://ng-bootstrap.github.io/#/components
- Use signals
- Use inject() rather than constructor injection
- Page components should be stored in the 'src/app/pages' folder and have a suffix of '.page.' and the classnames should end in Page.
- If there is a modal component that is specific to a single page, put it into that page's folder.
- Shared components, such as the navbar, should be stored in 'src/app/pages/shared' and have a suffix of '.component.' and the classnames should end in Component
- Guards should be stored in 'src/app/guards' and have a suffix of '.guard.ts' and the classnames should end in Guard
- Services should be stored in 'src/app/services' and have a suffix of '.service.ts' and the classnames should end in Service
- All OIDC integration should be done agnostic to the OIDC provider. It should allow for complete flexibility in which OIDC provider to use.
- when creating an interface representing a model, the id should always be prefixed with the domain object name. It shouldn't be just 'id'. ex
```typescript
export interface Account {
  accountId: string;
  name: string;
}
```

## Postgres

- The table names should be singular, not plural. Its the 'customer' table, not 'customers'.
  - If a 'user' table is needed, because user is a reserved word, use the table name 'users'. This is a special case. 
- The primary key should be the table name combined with 'Id' as a suffix. 'customer' has a 'customerId'. 'order' has an 'orderId'.
- Do not use UUID for PRIMARY KEY and FOREIGN KEY fields. 
- TEXT should be used for alphanumeric data, not VARCHAR.
- Use `created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP` and `updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP` where it makes sense
