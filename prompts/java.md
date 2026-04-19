# Java

- Use the latest LTS release for all new code
- Maven is the preferred build tool

- Records should be used as DTOs everywhere possible.
- Optional should be used for any nullable return types

## Validation

- Hibernate Validator @Valid annotations should be used on all appropriate service layer and repository layers method signatures.
- Validation messages are stored in 'src/main/resources/ValidationMessages.properties'