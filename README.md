# dev

Personal development standards and prompt files for LLM-assisted coding.

## Structure

```
business.md            # Master file - Red Hat employee context
personal.md            # Master file - Independent developer context
topics/
├── latest.md          # Current latest versions for shared technologies
├── general.md         # General guidance, preferences, OIDC
├── redhat.md          # Red Hat-specific context, product versions, product focus
├── openshift.md       # OpenShift, GitOps, syncwaves, Kustomize
├── java.md            # Java, Quarkus, Postgres, Flyway
├── angular.md         # Angular development standards
├── ansible.md         # Ansible conventions
├── containers.md      # Podman
└── github.md          # GitHub org layout, mkdocs, markdown
```

## Usage

At the start of a chat session, provide the LLM with the raw GitHub URL for the appropriate master file.

**Business (Red Hat employee)** - includes Red Hat product focus, OpenShift, Ansible, and all shared topics:

```
https://raw.githubusercontent.com/stephennimmo/dev/refs/heads/main/business.md
```

**Personal (independent developer)** - includes shared development topics without Red Hat-specific context:

```
https://raw.githubusercontent.com/stephennimmo/dev/refs/heads/main/personal.md
```

The LLM will fetch the master file, which instructs it to fetch and read each topic file listed within. This loads the full set of development standards into the conversation context.

Individual topic files can also be provided directly when only a specific subject is needed:

```
https://raw.githubusercontent.com/stephennimmo/dev/refs/heads/main/topics/java.md
```

## Adding a New Topic

1. Create a new file in `topics/`
2. Add its raw GitHub URL to `business.md`, `personal.md`, or both

## Updating Versions

Shared technology versions are in `topics/latest.md`. Red Hat product versions are in `topics/redhat.md`.
