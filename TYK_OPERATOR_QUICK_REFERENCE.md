# Tyk Operator Standalone - Quick Reference

## 🚀 Essential Commands

### 1. Add Helm Repository
```bash
helm repo add tyk-helm https://helm.tyk.io/public/helm/charts/
helm repo update
```

### 2. Create values-operator.yaml
```yaml
envVars:
  - name: TYK_OPERATOR_LICENSEKEY
    value: "YOUR_OPERATOR_LICENSE_KEY_HERE"
  - name: TYK_MODE
    value: "pro"
  - name: TYK_URL
    value: "http://dashboard-svc-tyk-cp-tyk-dashboard.tyk-cp.svc:3000"
  - name: TYK_AUTH
    value: "YOUR_API_KEY_HERE"
  - name: TYK_ORG
    value: "YOUR_ORG_ID_HERE"
envFrom: []
replicaCount: 1
```

### 3. Install Operator
```bash
kubectl create namespace tyk-cp
helm install tyk-operator tyk-helm/tyk-operator -n tyk-cp -f values-operator.yaml
```

### 4. Verify Installation
```bash
kubectl get pods -n tyk-cp | grep tyk-operator
kubectl logs -n tyk-cp deployment/tyk-operator-controller-manager | tail -5
```

### 5. Update Configuration
```bash
helm upgrade tyk-operator tyk-helm/tyk-operator -n tyk-cp -f values-operator.yaml
```

### 6. Uninstall
```bash
helm uninstall tyk-operator -n tyk-cp
```

## 🔧 Required Environment Variables

| Variable | Value |
|----------|-------|
| `TYK_OPERATOR_LICENSEKEY` | Your JWT license key |
| `TYK_MODE` | `"pro"` or `"ce"` |
| `TYK_URL` | Dashboard service URL |
| `TYK_AUTH` | API key from dashboard |
| `TYK_ORG` | Organization ID |

## 📍 URL Examples

- **Same namespace**: `http://dashboard-svc-tyk-cp-tyk-dashboard.tyk-cp.svc:3000`
- **Different namespace**: `http://dashboard-svc-tyk-cp-tyk-dashboard.tyk-cp.svc.cluster.local:3000`
- **External**: `https://your-dashboard.example.com` 