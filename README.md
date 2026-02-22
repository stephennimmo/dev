# dev

Personal development standards and prompt files for LLM-assisted coding.

## Structure

```
global.md              # Master file - references all topic files
topics/
├── latest.md          # Current latest versions for all technologies
├── general.md         # General guidance, preferences, OIDC
├── openshift.md       # OpenShift, GitOps, syncwaves, Kustomize
├── java.md            # Java, Quarkus, Postgres, Flyway
├── angular.md         # Angular development standards
├── ansible.md         # Ansible conventions
├── containers.md      # Podman
└── github.md          # GitHub org layout, mkdocs, markdown
```

## Usage

At the start of a chat session, provide the LLM with the raw GitHub URL for `global.md`:

```
https://raw.githubusercontent.com/stephennimmo/dev/refs/heads/main/global.md
```

The LLM will fetch `global.md`, which instructs it to fetch and read each topic file listed within. This loads the full set of development standards into the conversation context.

Individual topic files can also be provided directly when only a specific subject is needed:

```
https://raw.githubusercontent.com/stephennimmo/dev/refs/heads/main/topics/java.md
```

## Adding a New Topic

1. Create a new file in `topics/`
2. Add its raw GitHub URL to `global.md`

## Updating Versions

All version references are centralized in `topics/latest.md`. Update that single file when versions change.
