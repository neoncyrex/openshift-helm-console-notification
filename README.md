# Console Notification Helm Chart

## 📖 Description

This repository provides a GitOps-driven mechanism for deploying `ConsoleNotification` custom resources across OpenShift clusters using Helm and ArgoCD ApplicationSets. It supports both Non-Production and Production environments through environment-specific Helm values. CI/CD integration is implemented via GitHub Actions.

---

## 🎯 Purpose of Helm Chart

The `console-notification` chart manages the deployment of OpenShift `ConsoleNotification` resources that display banner messages in the web console. These notifications help communicate environment-specific or system-critical information to platform users.

---

## 🚀 How to Apply Manually

You can render and apply the Helm chart manually using the following steps:

```bash
# Clone the repository
git clone https://github.com/CHANGEME/openshift-helm-console-notification.git
cd openshift-helm-console-notification

# Lint
helm lint chart/

# Clone applicationset git repo
git clone https://github.com/CHANGEME/openshift-helm-applicationset-template.git
cd openshift-helm-applicationset-template

# Render
helm template main-applicationset ./main-applicationset -f ../applicationset/values.yaml > applicationset.yaml

# Apply to cluster
oc apply -f applicationset.yaml
```
---

## 👀 How to See the Result

1. Log in to the OpenShift web console.
2. You should see a colored banner at the top or bottom (depending on your Helm values).
3. Use `oc get consolenotifications` to validate the CR has been applied:

```bash
oc get consolenotifications
```

---

## 🔄 GitHub Workflow Integration

The `.github/workflows/ci.yaml` pipeline automates the rendering and application of Helm charts in the GitOps cluster. It can also optionally handle the creation of secrets required by the chart.

Key Features:
- Reusable pipeline integration
- Cluster targeting via environment
- GitOps ApplicationSet updates

---

## Helm Chart Variables

The following table outlines the configurable parameters used in the `ConsoleNotification` Helm chart and their default values:

| Variable         | Description                                 | Default Value       |
|------------------|---------------------------------------------|---------------------|
| `backgroundColor`| Background color of the banner              | `"#000000"`         |
| `color`          | Text color of the banner                    | `"#FFFFFF"`         |
| `text`           | The message text shown in the banner        | `"Environment Notice"` |

These values can be overridden via the appropriate `values-<environment>.yaml` file.

---

## ⚠️ Limitations

- Assumes ArgoCD and ApplicationSet controller are already deployed in the GitOps cluster.
- Only supports OpenShift environments with access to the GitOps instance.
- No native rollback or diff detection — relies on ArgoCD for state sync.

---

## 🔍 Post-Install Validation (Hooks)

The chart defines a post-install/upgrade test harness using a Pod hook (`pod_test.yaml`). It mounts `chart/files/tests/*` via a ConfigMap and runs `goss` assertions that verify:
* The Console operator reports the notification sync conditions are not Degraded or Progressing
* The `ConsoleNotification` custom resource exists with the expected name

Failures mark the Helm release as failed; ArgoCD will surface the sync error. Investigate by describing the failed hook Pod logs.

---

## 🛠️ TODO

None

---

## 📦 Prerequisites

- OpenShift 4.x with access to the OpenShift Console
- ArgoCD with ApplicationSet controller installed
- GitHub Actions configured with access to the GitOps cluster
- Helm 3.0+ installed locally for manual rendering

---

## 🧪 Testing Locally

You can validate the rendered manifest before applying:

- Clone the repository

```bash
# git clone https://github.com/CHANGEME/openshift-helm-console-notification.git
# cd openshift-helm-console-notification
# Clone the repository
git clone <NEW_REPO_URL>
cd openshift-helm-console-notification
```

- Clone ApplicationSet template repo

```bash
# git clone https://github.com/CHANGEME/openshift-helm-applicationset-template.git
# Clone ApplicationSet template repo
git clone <NEW_APPSET_TEMPLATE_REPO_URL>
```

- Test helm chart syntax

```bash
helm lint chart/
```

- Render ApplicationSet object

```bash
helm template openshift-helm-applicationset-template/main-applicationset \
  -f applicationset/values.yaml \
  -f applicationset/values-sbx.yaml > applicationset.yaml
```

- Apply to cluster

```bash
oc apply -f applicationset.yaml
```

---

## ✍️ Contribution Guidelines

Contributions, issues, and feature requests are welcome. Please fork the repository and submit a pull request following our branch naming conventions and review process.

---

## 📄 License 

This repository is licensed under the [MIT License](LICENSE), unless otherwise noted.

---
