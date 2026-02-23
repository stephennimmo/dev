# Angular
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

- Use the latest LTS release for all development
- Use ng-bootstrap components whereever possible - https://ng-bootstrap.github.io/#/components
- Use signals
- Use inject() rather than constructor injection
- Page components should be stored in the 'src/app/pages' folder and have a suffix of '.page.' and the classnames should end in Page.
- If there is a modal component that is specific to a single page, put it into that page's folder.
- Shared components, such as the navbar, should be stored in 'src/app/pages/shared' and have a suffix of '.component.' and the classnames should end in Component
- Guards should be stored in 'src/app/guards' and have a suffix of '.guard.ts' and the classnames should end in Guard
- Services should be stored in 'src/app/services' and have a suffix of '.service.ts' and the classnames should end in Service
- All OIDC integration should be done agnostic to the OIDC provider. It should allow for complete flexibility in which OIDC provider to use.