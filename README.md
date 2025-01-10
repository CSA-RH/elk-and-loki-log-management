# Log Management Repository

This repository provides configurations for setting up a log management solution using **Loki** and **ELK (Elasticsearch, Logstash, Kibana)** stacks with OpenShift GitOps (ArgoCD). It includes necessary YAML files to deploy both stacks, configure log forwarding, and integrate with OpenShift OAuth for secure Kibana access.

## Repository Structure

```plaintext
elk-and-loki-log-management/
├── applications/
│   ├── gitops-operator/
│   │   ├── gitops-subscription.yaml         # Install OpenShift GitOps Operator
│   ├── loki/
│   │   ├── loki-stack.yaml                  # Deploy Loki stack
│   │   └── loki-argocd-application.yaml     # ArgoCD Application for Loki
│   ├── elk/
│   │   ├── elasticsearch.yaml               # Deploy Elasticsearch
│   │   ├── kibana.yaml                      # Deploy Kibana with OAuth Proxy
│   │   ├── oauth-proxy.yaml                 # Configure OAuth Proxy for Kibana
│   │   └── elk-argocd-application.yaml      # ArgoCD Application for ELK stack
│   └── log-forwarding/
│       ├── clusterlogforwarder.yaml         # Configure OpenShift ClusterLogForwarder
│       └── log-forwarder-argocd-application.yaml # ArgoCD Application for log forwarding
└── README.md                                # Documentation for the repository
```

## Prerequisites

1. OpenShift 4.15+ cluster.

## Getting Started

### Step 1: Clone the Repository
Clone this repository to your local machine:
```bash
git clone git@github.com:CSA-RH/elk-and-loki-log-management.git
cd elk-and-loki-log-management
```

### Step 2: Deploy OpenShift GitOps Operator
The GitOps operator enables ArgoCD for managing your applications.

1. Deploy the GitOps operator using the provided YAML:
   ```bash
   oc apply -f applications/gitops-operator/gitops-subscription.yaml
   ```
2. Verify the operator is installed:
   ```bash
   oc get pods -n openshift-gitops-operator
   ```
3. Verify the ArgoCD instance is running:
   ```bash
   oc get pods -n openshift-gitops
   ```

### Step 3: Deploy Loki Stack
1. Add the Loki stack application to ArgoCD:
   ```bash
   oc apply -f applications/loki/loki-argocd-application.yaml
   ```
2. Monitor the ArgoCD dashboard to ensure Loki is deployed successfully.

### Step 4: Deploy ELK Stack
1. Add the ELK stack application to ArgoCD:
   ```bash
   oc apply -f applications/elk/elk-argocd-application.yaml
   ```
2. Verify the Elasticsearch and Kibana pods are running:
   ```bash
   oc get pods -n logging
   ```

### Step 5: Configure Log Forwarding
1. Deploy the log forwarding configuration:
   ```bash
   oc apply -f applications/log-forwarding/log-forwarder-argocd-application.yaml
   ```
2. Logs will now be forwarded to both Loki and Elasticsearch.

## Viewing Logs

### Loki Logs
1. Deploy Grafana to visualize logs from Loki.
2. Add Loki as a data source in Grafana:
   - URL: `http://loki-stack.logging.svc:3100`.

### Kibana Logs
1. Access Kibana at the route created during deployment:
   ```bash
   oc get route kibana -n logging
   ```
2. Log in using your OpenShift credentials (OAuth proxy is configured).

## Customization

- Modify the `clusterlogforwarder.yaml` to adjust log forwarding rules.
- Update resource requests/limits in Loki and Elasticsearch deployments to optimize resource usage.

## Troubleshooting

1. **Log Forwarder Issues**:
   - Check the `ClusterLogForwarder` status:
     ```bash
     oc get clusterlogforwarder -n openshift-logging
     ```
2. **Loki or ELK Deployment Issues**:
   - Inspect logs of the pods:
     ```bash
     oc logs <pod-name> -n logging
     ```

## Contributing

Feel free to contribute by creating pull requests or opening issues for bugs or feature requests.

