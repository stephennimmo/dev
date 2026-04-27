# AGENTS

## General

- If there is documentation or a README present, if changes are made, please update the documentation.
- In the root of projects, use `AGENTS.md` as the primary file for managing LLM context. This is the file where the full condensed history of the interactions and prompts should be stored.
- Preferred cloud environment is AWS, us-east-2 region
- YAML files end in .yaml, not .yml
- Use Quad9 for upstream DNS - 9.9.9.9, 149.112.112.112
- Authentication and authorization should be OIDC using JWT tokens.
- The reference OIDC provider should be Keycloak, but should be agnostic to any OIDC provider.
- Absolutely no sensitive information should be stored in any git repository

## Red Hat

### Red Hat Context

- All solutions should be focused on Red Hat products or partners including IBM, Hashicorp, Confluent, etc
- All bash commands should be RHEL/Fedora compatible (use dnf, etc)
- All references to Kubernetes should assume OpenShift as a target environment
- The latest stable release of OpenShift should be used
- Automations should be built using Ansible
- Always source containers from Red Hat sources such as registry.redhat.io and quay.io

### Red Hat Branding

- Use the following information to create any artifacts related to documentation
  - https://www.redhat.com/en/about/brand/standards
  - https://www.redhat.com/en/about/brand/standards/color
  - https://www.redhat.com/en/about/brand/standards/typography

## Java

- Use the latest LTS release for all new code
- Maven is the preferred build tool
- Records should be used as DTOs everywhere possible.
- Optional should be used for any nullable return types

### Validation

- Hibernate Validator @Valid annotations should be used on all appropriate service layer and repository layers method signatures.
- Validation messages are stored in 'src/main/resources/ValidationMessages.properties'

## Quarkus

### General Guidance

- Configuration should be in YAML and follow the "%dev", "%test", "%prod" structure.
- All OIDC integration should be done agnostic to the OIDC provider. It should allow for complete flexibility in which OIDC provider to use.
- Turn off banner in quarkus configuration

### REST API

- Use openapi specification for all REST endpoints
- REST API development should follow the three tier structure: repository, service, api. Example packages: com.examplecompany.${project_name}.{api|service|repository}
    - The api package should contain all Resource classes as well as the Request and Response records used to define the API. The Resource classes should primarily interact with the service layer.
    - The service package should contain the Service classes and all the Domain records. All public methods on Service classes should produce and consume Domain records. The service layer should only interact with the repository layer.
    - The repository package should contain the Repository classes as well as the Entity classes. All public methods on Repository classes should produce and consume Entity classes.
- All data access should be performed using the repository layer and should only be accessed through a service layer.
- The service layer should consume and return only domain objects.
- The `@RolesAllowed` annotations should always be at the method level
- Put roles into an enum called Roles and use that in the @RolesAllowed instead of strings.
- Resource class methods for API endpoints should always return `jakarta.ws.rs.core.Response` objects with the correct HTTP status code.
- Any generated Ids should follow the name entityId, not just id. ex, Person has personId, Bill has billId, Premise has premiseId.

### Panache Framework

- The JPA Entity classes should follow the rules:
  - Suffix should be 'Entity'. ex. PersonEntity, CarEntity
  - Class annotations should always include the @Entity(name="Entity") and @Table(name="entity") to specify the name of the entity and the exact table.
  - Id should follow the pattern of TypeId, not just id. For example, for a Person, the entity would be PersonEntity and the id would be personId and the database column should be person_id
  - Should always include @Column with name attribute specified and any nullable information based on the schema
  - jakarta validations should be used to validate the individual fields and should reference validation messages
  - Fields should be public
  - HashCode, equals and toString should be generated and present
- Repository pattern should be used

### Protobuf and gRPC

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

### Quinoa

- Always put Quinoa based web projects in `src/main/webui`
- The only two additions needed for the Quinoa to run are the following:
    - `yq -i '.quarkus.quinoa.enable-spa-routing = true' src/main/resources/application.yml`
    - `yq -i '.quarkus.quinoa.build-dir = "dist/'$NG_PROJECT_NAME'/browser"' src/main/resources/application.yml`

### Flyway Database Schema

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

## Postgres

- The table names should be singular, not plural. Its the 'customer' table, not 'customers'.
- The primary key should be the table name combined with 'Id' as a suffix. 'customer' has a 'customerId'. 'order' has an 'orderId'.
- Always use BIGSERIAL and BIGINT for PRIMARY KEY and FOREIGN KEY fields. Do not use UUID.
- Always set the starting sequence values - ALTER SEQUENCE customer_customer_id_seq RESTART 1000000000000000000;
- Generated sequences from BIGSERIAL and SERIAL fields should also be set to a number to fill all the digits - ALTER SEQUENCE customer_customer_id_seq RESTART 1000000000000000000;
- TEXT should be used for alphanumeric data, not VARCHAR.
- Use `created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP` and `updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP` where it makes sense

## Containers

- Assume Podman
- Use `Containerfile` not `Dockerfile`
- Don't use version in Podman Compose files

## Ansible

- YAML files end in .yaml, not .yml
- Use a python venv at .venv for ansible execution
- Use FQCN for builtin module actions

## GitHub Organization

- A github organization is represented as a folder at `~/projects/github/${organization_name}`
- Repositories are located at `~/projects/github/${organization_name}/${repository_name}`
- Repository names should use the following prefixes:
  - `api-` — Clear for deployable backends (api-account, api-enrollment). Keeps service repos distinct from frontends and platform repos.
  - `portal-` — authenticated apps (customer, partner, internal).
  - `web-` - marketing sites, docs, or mobile shells
  - `gitops-` — OpenShift GitOps layout (Argo CD, Kustomize, sync waves). Good for cluster and application GitOps repos if you name them so the role is obvious, e.g. gitops-cluster / gitops-apps or gitops-openshift-cluster / gitops-examplerep-apps.
  - `iac-` — Good for non-GitOps infra automation: Terraform for AWS accounts/networking, Ansible Automation Platform content, etc.
- A typical organization has a documentation site located in the repository named `${organization_name}.github.io`. See Documentation Site below for more information.

### Documentation Site

- Documentation sites are created using material for mkdocs - https://squidfunk.github.io/mkdocs-material
- Create a new documentation site using these instructions: https://squidfunk.github.io/mkdocs-material/creating-your-site
- Documentation sites are hosted on github using github actions - https://squidfunk.github.io/mkdocs-material/publishing-your-site/#with-github-actions
- Use Python virtual environments (.venv) for any docs site
- Always start mkdocs with --livereload

### Markdown

- When creating markdown tables, use spaces to make the tables readable in raw format
- Use only three dashes - `---` - to create lines for tables.

## OpenShift

- Use the latest version of OpenShift when performing any install
- Use OpenShift GitOps for all cluster configurations post-install. https://docs.redhat.com/en/documentation/red_hat_openshift_gitops/latest
- Secrets should be handled using ExternalSecretsOperator. https://docs.redhat.com/en/documentation/openshift_container_platform/latest/html/security_and_compliance/external-secrets-operator-for-red-hat-openshift
- Red Hat Service Mesh
- OpenShift Pipelines
- Red Hat Developer Hub
- Red Hat Connectivity Link
- Pull secret is at `~/.pull-secret`
- OpenShift nodes should be `control-plane`, `worker` and `infrastructure`. Never use `master`

## OpenShift GitOps

- The gitops repository has the following structure:
    - applications - contains the argocd application manifests and the app-of-apps root application
    - namespace folders - contains the manifests associated with the namespace, needs to use syncwaves if operators proceed CRs
- There should be two types of repositories: `cluster` and `application`
- `cluster` repositories take a fresh cluster to being production ready.
- `application` repositories deploy workloads after the cluster repositories are applied.
- Always use syncwaves for all manifests.
- Always use Kustomize.
- syncwaves start at 0 and are typically namespace manifests.
- For any custom resource dependencies, here's an example of the use of syncwaves.
    - namespace: 0
    - operator-group: 1
    - subscription: 2
    - custom resource: 3
    - custom resource: 4
    - etc
- In the kustomization.yaml file, if the resources have syncwaves, put them in syncwave order and add a comment at the end of the line with the syncwave number. ex. # sync-wave-1
- Example repository structure:
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
