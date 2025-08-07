# Tyk Operator Standalone Deployment Guide

This guide explains how to deploy the Tyk Operator as a standalone component using environment variables instead of Kubernetes secrets.

## 📋 Prerequisites

- Kubernetes cluster with kubectl access
- Helm 3.x installed
- Tyk Dashboard already deployed and accessible
- Tyk Operator license key
- API key and Org ID from Tyk Dashboard

## 🚀 Quick Start

### 1. Add Tyk Helm Repository

```bash
helm repo add tyk-helm https://helm.tyk.io/public/helm/charts/
helm repo update
```

### 2. Create Values File

Create a file named `values-operator.yaml` with the following content:

```yaml
# Tyk Operator standalone deployment values
envVars:
  - name: TYK_OPERATOR_LICENSEKEY
    value: "YOUR_OPERATOR_LICENSE_KEY_HERE"
  - name: TYK_MODE
    value: "pro"  # Use "ce" for Community Edition, "pro" for Pro/Licensed
  - name: TYK_URL
    value: "http://dashboard-svc-tyk-cp-tyk-dashboard.tyk-cp.svc:3000"
  - name: TYK_AUTH
    value: "YOUR_API_KEY_HERE"
  - name: TYK_ORG
    value: "YOUR_ORG_ID_HERE"

# Remove the secret reference since we're using environment variables
envFrom: []

replicaCount: 1
```

### 3. Install Tyk Operator

```bash
# Create namespace if it doesn't exist
kubectl create namespace tyk-cp

# Install the operator
helm install tyk-operator tyk-helm/tyk-operator -n tyk-cp -f values-operator.yaml
```

## 🔧 Environment Variables Reference

| Variable | Required | Description | Example |
|----------|----------|-------------|---------|
| `TYK_OPERATOR_LICENSEKEY` | Yes | JWT-encoded Tyk Operator license key | `eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...` |
| `TYK_MODE` | Yes | Operator mode | `"pro"` for licensed, `"ce"` for community |
| `TYK_URL` | Yes | Dashboard/Gateway management URL | `http://dashboard-svc-tyk-cp-tyk-dashboard.tyk-cp.svc:3000` |
| `TYK_AUTH` | Yes | Operator user API key | `2d095c2155774fe36d77e5cbe3ac963b` |
| `TYK_ORG` | Yes | Organization ID | `6894ab024b8c2f00018b4e2e` |

## 📍 URL Configuration Examples

### Same Namespace
If the operator is in the same namespace as the dashboard:
```yaml
TYK_URL: "http://dashboard-svc-tyk-cp-tyk-dashboard.tyk-cp.svc:3000"
```

### Different Namespace
If the operator is in a different namespace:
```yaml
TYK_URL: "http://dashboard-svc-tyk-cp-tyk-dashboard.tyk-cp.svc.cluster.local:3000"
```

### External Dashboard
If the dashboard is accessible externally:
```yaml
TYK_URL: "https://your-dashboard.example.com"
```

## 🔍 Verification

### Check Operator Status
```bash
kubectl get pods -n tyk-cp | grep tyk-operator
```

Expected output:
```
tyk-operator-controller-manager-xxxxx-xxxxx    1/1     Running   0          30s
```

### Check Operator Logs
```bash
kubectl logs -n tyk-cp deployment/tyk-operator-controller-manager | tail -10
```

Look for successful connection messages:
```
{"level":"info","msg":"Call","Method":"GET","URL":"http://dashboard-svc-tyk-cp-tyk-dashboard:3000/api/...","Status":200}
```

## 🛠️ Troubleshooting

### Common Issues

1. **Connection Failed**
   - Verify the `TYK_URL` is correct
   - Check if the dashboard is accessible from the operator namespace
   - Ensure the dashboard service name matches

2. **Authentication Failed**
   - Verify the `TYK_AUTH` API key is valid
   - Check if the `TYK_ORG` ID is correct
   - Ensure the API key has operator permissions

3. **License Issues**
   - Verify the `TYK_OPERATOR_LICENSEKEY` is valid
   - Check if the license is not expired
   - Ensure the license is for the correct Tyk version

## 🔄 Updating Configuration

### Update Environment Variables
```bash
# Edit the values file
vim values-operator.yaml

# Upgrade the deployment
helm upgrade tyk-operator tyk-helm/tyk-operator -n tyk-cp -f values-operator.yaml
```

## 🧹 Cleanup

### Uninstall Operator
```bash
helm uninstall tyk-operator -n tyk-cp
```

### Remove Namespace (if empty)
```bash
kubectl delete namespace tyk-cp
```
