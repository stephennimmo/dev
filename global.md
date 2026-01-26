# General Guidance
- All solutions should be focused on Red Hat products or partners
    - IBM, Hashicorp, Confluent
- All bash commands should be RHEL/Fedora compatible (use dnf, etc)
- All references to Kubernetes should assume OpenShift as a target environment
    - The latest stable release of OpenShift should be used.
- Automations should be build using Ansible
- Preferred cloud environment is AWS, us-east-2 region
- YAML files end in .yaml, not .yml
- Use Quad9 for upstream DNS - 9.9.9.9, 149.112.112.112

## Ansible
- YAML files end in .yaml, not .yml
- Use a python venv at .venv for ansible execution

## OpenShift
- Use OpenShift gitops for all cluster configurations
    - One repo for cluster configurations and getting gitops bootstrapped
    - One repo for application configurations
- Secrets should be handled using ExternalSecretsOperator: https://external-secrets.io/latest
- Red Hat Service Mesh
- OpenShift Pipelines
- Red Hat Developer Hub
- Red Hat Connectivity Link
- Pull secret is at `~/.pull-secret`

## GitOps
- The gitops repository has the following structure:
    - applications - contains the argocd application manifests and the app-of-apps root application
    - namespace folders - contains the manifests associated with the namespace, needs to use syncwaves if operators proceed CRs
- There should be two types of repositories: cluster and application
- cluster repositories take a fresh cluster to being production ready.
- application repositories deploy workloads after the cluster repositories are applied.
- syncwaves start at 0 and are typically namespace manifests.
    - namespace: 0
    - operator-group: 1
    - subscription: 2
- In the kustomization.yaml file, if the resources have syncwaves, put them in syncwave order and add a comment at the end of the line with the syncwave number. ex. # sync-wave-1

```
gitops-repository/
├── app-of-apps.yaml               # Root application
├── applications/                  # ArgoCD Application manifests
│   ├── kustomization.yaml
│   ├── folder1.yaml               # sync-wave-0
│   ├── folder3.yaml               # sync-wave-1
│   └── folder2.yaml               # sync-wave-2
├── folder1/                           
│   ├── kustomization.yaml
│   └── resource.yaml              # sync-wave-0
├── folder2/                           
│   ├── kustomization.yaml
│   └── resource.yaml              # sync-wave-0
├── folder3/                           
│   ├── kustomization.yaml
│   └── resource.yaml              # sync-wave-0
└── README.md
```


## Github Organization
- A github organization is represented as a folder at `~/projects/github/${organization_name}`
- Repositories are located at `~/projects/github/${organization_name}/${repository_name}`
- A typical organization has a documentation site located in the repository named `${organization_name}.github.io`. See Documentation Site below for more information.
- Absolutely no sensitive information should be stored in any git repository

# Development
- Authentication and authorization should be OIDC using JWT tokens.
- The reference OIDC provider should be Keycloak, but should be agnostic to any OIDC provider.

## Angular
- Use the latest lts release for all development - which is angular version 21. 
- Use ng-bootstrap components whereever possible - https://ng-bootstrap.github.io/#/components/accordion/overview
- Use signals
- Use inject() rather than constructor injection
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
## Java
- Use the latest LTS release for all new code - Java 25
- Maven is the preferred build tool

### Quarkus
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

#### Quarkus - REST API
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

#### Quarkus - Panache Framework
- The JPA Entity classes should follow the rules:
  - Suffix should be 'Entity'. ex. PersonEntity, CarEntity
  - Class annotations should always include the @Entity(name="Entity") and @Table(name="entity") to specify the name of the entity and the exact table.
  - Id should follow the pattern of TypeId, not just id. For example, for a Person, the entity would be PersonEntity and the id would be personId and the database column should be person_id
  - Should always include @Column with name attribute specified and any nullable information based on the schema
  - jakarta validations should be used to validate the individual fields and should reference validation messages
  - Fields should be public
  - HashCode, equals and toString should be generated and present
- Repository pattern should be used

#### Quarkus - Postgres and Flyway Database Schema
- Use Flyway for all schema management
    - Test Data for Flyway should be placed in a file named `src/main/resources/db/testdata/V999__testdata.sql`
    - dev and test profiles should include the `db/testdata` folder with the `locations: db/migration,db/testdata` but not prod
- Test data should be generated using primary key values lower than the starting sequence to easily be able to delete them

#### Quarkus - Protobuf and gRPC
- Use proto3
- Enum definitions always have an `UNSPECIFIED` as the 0 value
- All values should be prefixed with the all caps snake case vaue of the name of the enum
- Example
```
enum ConnectionType {
  CONNECTION_TYPE_UNSPECIFIED = 0;
  CONNECTION_TYPE_INITIATOR = 1;
  CONNECTION_TYPE_ACCEPTOR = 2;
}
```

### Postgres
- The table names should be singular, not plural. Its the 'customer' table, not 'customers'.
- The primary key should be the table name combined with 'Id' as a suffix. 'customer' has a 'customerId'. 'order' has an 'orderId'.
- Always use BIGSERIAL and BIGINT for PRIMARY KEY and FOREIGN KEY fields. Do not use UUID.
- Always set the starting sequence values - ALTER SEQUENCE customer_customer_id_seq RESTART 1000000000000000000;
- Generated sequences from BIGSERIAL and SERIAL fields should also be set to a number to fill all the digits - ALTER SEQUENCE customer_customer_id_seq RESTART 1000000000000000000;
- TEXT should be used for alphanumeric data, not VARCHAR.
- Use `created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP` and `updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP` where it makes sense

### Documentation Site

- Documentation sites are created using material for mkdocs - https://squidfunk.github.io/mkdocs-material
- Create a new documentation site using these instructions: https://squidfunk.github.io/mkdocs-material/creating-your-site 
- Documentation sites are hosted on github using github actions - https://squidfunk.github.io/mkdocs-material/publishing-your-site/#with-github-actions

#### Markdown
- When creating markdown tables, use spaces to make the tables readable in raw format
