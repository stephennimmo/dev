# Github Organization
- A github organization is represented as a folder at `~/projects/github/${organization_name}`
- Repositories are located at `~/projects/github/${organization_name}/${repository_name}`
- Repository names should use the following prefixes:
  - `api-` — Clear for deployable backends (api-account, api-enrollment). Keeps service repos distinct from frontends and platform repos.
  - `portal-` — authenticated apps (customer, partner, internal).
  - `web-` - marketing sites, docs, or mobile shells
  - `gitops-` — OpenShift GitOps layout (Argo CD, Kustomize, sync waves). Good for cluster and application GitOps repos if you name them so the role is obvious, e.g. gitops-cluster / gitops-apps or gitops-openshift-cluster / gitops-examplerep-apps.
  - `iac-` — Good for non-GitOps infra automation: Terraform for AWS accounts/networking, Ansible Automation Platform content, etc.
- A typical organization has a documentation site located in the repository named `${organization_name}.github.io`. See Documentation Site below for more information.

## Documentation Site
- Documentation sites are created using material for mkdocs - https://squidfunk.github.io/mkdocs-material
- Create a new documentation site using these instructions: https://squidfunk.github.io/mkdocs-material/creating-your-site
- Documentation sites are hosted on github using github actions - https://squidfunk.github.io/mkdocs-material/publishing-your-site/#with-github-actions
- Use Python virtual environments (.venv) for any docs site
- Always start mkdocs with --livereload

## Markdown
- When creating markdown tables, use spaces to make the tables readable in raw format
- Use only three dashes - `---` - to create lines for tables. 
