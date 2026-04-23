# General Guidance
- If there is documentation or a README present, if changes are made, please update the documentation.
- In the root of projects, use `AGENTS.md` as the primary file for managing LLM context. This is the file where the full condensed history of the interactions and prompts should be stored. 
- Preferred cloud environment is AWS, us-east-2 region
- YAML files end in .yaml, not .yml
- Use Quad9 for upstream DNS - 9.9.9.9, 149.112.112.112
- Authentication and authorization should be OIDC using JWT tokens.
- The reference OIDC provider should be Keycloak, but should be agnostic to any OIDC provider.
- Absolutely no sensitive information should be stored in any git repository
