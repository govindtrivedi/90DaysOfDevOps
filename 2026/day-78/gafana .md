## Day 78 — Helm: what you should understand and implement

The core idea of today is:

> **Raw Kubernetes YAML describes one deployment. Helm turns those manifests into a reusable, configurable, versioned package.**

### 1. Helm concepts

| Concept        | Meaning                                                     |
| -------------- | ----------------------------------------------------------- |
| **Chart**      | A package containing Kubernetes templates and configuration |
| **Release**    | A deployed instance of a chart                              |
| **Repository** | A location where Helm charts are published                  |
| **Values**     | Configuration used to customize a chart                     |

For the AI-BankApp, this is particularly useful because the current `k8s/` directory contains **12 separate manifests**. Instead of maintaining different copies for dev/staging/prod, one Helm chart can use different values files.

For example:

```text
AI-BankApp
       |
       v
   Helm Chart
       |
   +---+---+
   |   |   |
 dev  stage prod
values values values
```

---

## 2. Set up Kind + Helm

From your terminal:

```bash
git clone -b feat/gitops https://github.com/TrainWithShubham/AI-BankApp-DevOps.git
cd AI-BankApp-DevOps

kind create cluster --config setup-k8s/kind-config.yml
```

Then install/verify Helm:

```bash
helm version
kubectl cluster-info
helm list
```

Check the existing manifests:

```bash
ls k8s/
```

You should see the collection of resources described in the task, including Deployments, Services, ConfigMaps, Secrets, PVC/PV, HPA, Gateway, and cert-manager configuration.

---

# 3. Deploy MySQL with Helm

Add Bitnami:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

Search:

```bash
helm search repo bitnami/mysql
```

Install:

```bash
helm install bankapp-mysql bitnami/mysql \
  --set auth.rootPassword='Test@123' \
  --set auth.database=bankappdb \
  --set primary.resources.requests.memory=256Mi \
  --set primary.resources.requests.cpu=250m \
  --set primary.resources.limits.memory=512Mi \
  --set primary.resources.limits.cpu=500m \
  --set primary.persistence.size=5Gi
```

Then inspect the release:

```bash
helm list
helm status bankapp-mysql
```

And Kubernetes resources:

```bash
kubectl get all -l app.kubernetes.io/instance=bankapp-mysql
kubectl get pvc -l app.kubernetes.io/instance=bankapp-mysql
kubectl get secret -l app.kubernetes.io/instance=bankapp-mysql
```

### Important Helm distinction

The command:

```bash
helm install bankapp-mysql bitnami/mysql
```

creates a **release** named `bankapp-mysql` from the `bitnami/mysql` **chart**.

You can therefore deploy the same chart multiple times:

```text
bitnami/mysql
     |
     +--> bankapp-mysql-dev
     |
     +--> bankapp-mysql-staging
     |
     +--> bankapp-mysql-prod
```

Each release can have different values.

---

# 4. Use a values file

Create `mysql-values.yaml`:

```yaml
auth:
  rootPassword: Test@123
  database: bankappdb

primary:
  resources:
    limits:
      cpu: 500m
      memory: 512Mi
    requests:
      cpu: 250m
      memory: 256Mi

  persistence:
    size: 5Gi
    storageClass: ""

metrics:
  enabled: true
  serviceMonitor:
    enabled: false
```

This is preferable to a huge collection of `--set` arguments because your configuration becomes version-controlled and reviewable.

Deploy another release:

```bash
helm install bankapp-mysql-v2 bitnami/mysql \
  -f mysql-values.yaml
```

Inspect:

```bash
helm list
```

Remove the test release afterward:

```bash
helm uninstall bankapp-mysql-v2
```

---

# 5. Understand the values hierarchy

A useful mental model is:

```text
Chart defaults
      |
      v
values.yaml
      |
      v
your-values.yaml
      |
      v
--set overrides
      |
      v
Rendered Kubernetes YAML
      |
      v
Kubernetes API
```

For example, the chart might contain something conceptually like:

```yaml
image:
  repository: mysql
  tag: "8.0"
```

and a template:

```yaml
image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```

If you provide:

```bash
--set image.tag=8.4
```

Helm substitutes that value while rendering the Kubernetes manifest.

You can see the rendered result without installing anything:

```bash
helm template bankapp-mysql bitnami/mysql \
  -f mysql-values.yaml
```

This is one of the most useful Helm debugging commands.

---

# 6. Upgrade and rollback

Upgrade the existing release:

```bash
helm upgrade bankapp-mysql bitnami/mysql \
  --set auth.rootPassword='Test@123' \
  --set auth.database=bankappdb \
  --set metrics.enabled=true
```

Check history:

```bash
helm history bankapp-mysql
```

You should see revisions similar to:

```text
REVISION   STATUS
1          superseded
2          deployed
```

Rollback:

```bash
helm rollback bankapp-mysql 1
```

Then:

```bash
helm history bankapp-mysql
```

A key point: rollback doesn't erase history. Helm creates another revision representing the rollback.

Conceptually:

```text
Revision 1
    |
    | upgrade
    v
Revision 2
    |
    | rollback to 1
    v
Revision 3
```

This is a major advantage over manually maintaining and re-applying raw Kubernetes YAML.

---

# 7. Chart structure

Pull the chart locally:

```bash
helm pull bitnami/mysql --untar
```

Then:

```bash
ls mysql/
```

The important structure is:

```text
mysql/
├── Chart.yaml
├── values.yaml
├── charts/
└── templates/
    ├── primary/
    │   ├── statefulset.yaml
    │   └── svc.yaml
    ├── _helpers.tpl
    ├── NOTES.txt
    └── secrets.yaml
```

### `Chart.yaml`

Contains chart metadata.

The two fields you specifically need to understand are:

```yaml
version: 12.2.1
appVersion: "8.0.40"
```

**`version`** = version of the **Helm chart itself**.

It changes when the chart/templates/configuration change.

**`appVersion`** = version of the **application being packaged**.

So, conceptually:

```text
Chart version: 12.2.1
        |
        +---- packages/configures ----> MySQL 8.0.40
```

They are therefore not the same thing.

---

# 8. Raw YAML vs Helm

For your AI-BankApp, this is the important comparison:

| Area             | Raw Kubernetes YAML     | Helm                      |
| ---------------- | ----------------------- | ------------------------- |
| Configuration    | Hardcoded               | Values                    |
| Environments     | Separate/manual changes | Different values files    |
| Packaging        | Multiple manifests      | One chart                 |
| Versioning       | Git/manual              | Chart + release revisions |
| Rollback         | Manual                  | `helm rollback`           |
| Dependencies     | Manually managed        | Helm dependencies         |
| Reuse            | Limited                 | High                      |
| Community charts | No                      | Yes                       |

For example, instead of:

```text
k8s/
├── mysql-deployment.yml
├── secrets.yml
├── pvc.yml
├── pv.yml
└── service.yml
```

you eventually want something conceptually like:

```text
helm/
└── bankapp/
    ├── Chart.yaml
    ├── values.yaml
    ├── values-dev.yaml
    ├── values-prod.yaml
    └── templates/
        ├── deployment.yaml
        ├── service.yaml
        ├── configmap.yaml
        ├── secret.yaml
        ├── pvc.yaml
        └── ...
```

Then:

```bash
helm upgrade --install bankapp ./helm/bankapp \
  -f ./helm/bankapp/values-dev.yaml
```

and production can use:

```bash
helm upgrade --install bankapp ./helm/bankapp \
  -f ./helm/bankapp/values-prod.yaml
```

**Same chart, different configuration.**

---

# 9. Your Day 78 documentation

Create:

```bash
mkdir -p 2026/day-78
touch 2026/day-78/day-78-helm-intro.md
```

A good structure for the Markdown file is:

````markdown
# Day 78 — Introduction to Helm and Chart Basics

## What is Helm?

Helm is a package manager for Kubernetes. It packages Kubernetes
resources into reusable, configurable charts.

## Core Concepts

### Chart
...

### Release
...

### Repository
...

### Values
...

## Raw YAML vs Helm

| Aspect | Raw YAML | Helm |
|---|---|---|
| ... | ... | ... |

## MySQL Deployment

### Helm command

```bash
helm install ...
````

### Values file

```yaml
...
```

## Chart Structure

```text
mysql/
├── Chart.yaml
├── values.yaml
├── charts/
└── templates/
```

## Chart.yaml

### version

The version of the Helm chart.

### appVersion

The version of the application packaged by the chart.

## Helm Release Management

### Install

```bash
helm install ...
```

### Upgrade

```bash
helm upgrade ...
```

### History

```bash
helm history ...
```

### Rollback

```bash
helm rollback ...
```

### Uninstall

```bash
helm uninstall ...
```

## Why AI-BankApp Benefits from Helm

The application currently has 12 Kubernetes manifests.
Helm can consolidate these into one reusable chart with
environment-specific values.

## Screenshots

* `helm list`
* `helm history bankapp-mysql`
* Kubernetes resources
* Chart structure

## Key Takeaways

1. Helm packages Kubernetes resources.
2. Charts are reusable.
3. Releases represent installed instances.
4. Values customize deployments.
5. Helm provides upgrade and rollback capabilities.

````

---

## One important thing to watch

Your task text uses **Bitnami MySQL** and specific chart values, but Helm repositories and chart versions evolve. If a current Bitnami chart rejects one of those values or has changed its structure, don't blindly force the old command to work. Check:

```bash
helm show values bitnami/mysql
````

and:

```bash
helm show chart bitnami/mysql
```

Then adapt the values to the chart version actually returned by your repository.

### Final Day 78 checklist

```text
☐ Kind cluster running
☐ Helm installed
☐ Helm connected to Kubernetes
☐ Bitnami repository added
☐ MySQL deployed with Helm
☐ mysql-values.yaml created
☐ Second release tested
☐ Upgrade performed
☐ helm history inspected
☐ Rollback performed
☐ Chart pulled and structure explored
☐ version vs appVersion understood
☐ day-78-helm-intro.md created
☐ Screenshots added
☐ Changes committed and pushed
```

For tomorrow, **don't delete your AI-BankApp repository**. Day 79 is where the useful part begins: turning those 12 hardcoded Kubernetes manifests into your own reusable Helm chart.
