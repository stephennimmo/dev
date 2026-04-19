# OpenShift
- Use the latest version of OpenShift when performing any install
- Use OpenShift GitOps for all cluster configurations post-install. https://docs.redhat.com/en/documentation/red_hat_openshift_gitops/latest
- Secrets should be handled using ExternalSecretsOperator. https://docs.redhat.com/en/documentation/openshift_container_platform/latest/html/security_and_compliance/external-secrets-operator-for-red-hat-openshift
- Red Hat Service Mesh
- OpenShift Pipelines
- Red Hat Developer Hub
- Red Hat Connectivity Link
- Pull secret is at `~/.pull-secret`
- OpenShift nodes should be `control-plane`, `worker` and `infrastructure`. Never use `master`