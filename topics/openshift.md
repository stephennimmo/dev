# OpenShift
- Use the latest version of OpenShift when performing any install
- Use OpenShift GitOps for all cluster configurations post-install. https://docs.redhat.com/en/documentation/red_hat_openshift_gitops/latest
- Secrets should be handled using ExternalSecretsOperator. https://docs.redhat.com/en/documentation/openshift_container_platform/latest/html/security_and_compliance/external-secrets-operator-for-red-hat-openshift
- Red Hat Service Mesh
- OpenShift Pipelines
- Red Hat Developer Hub
- Red Hat Connectivity Link
- Pull secret is at `~/.pull-secret`

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
