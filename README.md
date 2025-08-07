# Tyk Operator Standalone

A complete setup for deploying Tyk API Gateway Operator in Kubernetes using environment variables instead of Kubernetes secrets.

## 📋 Overview

This repository contains everything needed to deploy and manage Tyk API Gateway Operator in a Kubernetes environment. The setup uses environment variables for configuration, making it easier to deploy in various environments without managing Kubernetes secrets.

## 🚀 Quick Start

### Prerequisites

- Kubernetes cluster with kubectl access
- Helm 3.x installed
- Tyk Dashboard already deployed and accessible
- Tyk Operator license key
- API key and Org ID from Tyk Dashboard

### Installation

1. **Clone this repository**
   ```bash
   git clone <your-repo-url>
   cd tyk-operator-standalone
   ```

2. **Configure the operator**
   ```bash
   # Copy the template and edit with your values
   cp values-operator.template.yaml values-operator.yaml
   # Edit values-operator.yaml with your actual values
   ```

3. **Deploy the operator**
   ```bash
   # Add Tyk Helm repository
   helm repo add tyk-helm https://helm.tyk.io/public/helm/charts/
   helm repo update
   
   # Create namespace
   kubectl create namespace tyk-cp
   
   # Install the operator
   helm install tyk-operator tyk-helm/tyk-operator -n tyk-cp -f values-operator.yaml
   ```

4. **Verify installation**
   ```bash
   kubectl get pods -n tyk-cp | grep tyk-operator
   kubectl logs -n tyk-cp deployment/tyk-operator-controller-manager | tail -5
   ```

## 📁 Repository Structure

```
tyk-operator-standalone/
├── README.md                           # This file
├── .gitignore                          # Git ignore rules
├── values-operator.template.yaml       # Template for operator configuration
├── TYK_OPERATOR_STANDALONE_GUIDE.md    # Comprehensive deployment guide
├── TYK_OPERATOR_QUICK_REFERENCE.md     # Quick reference commands
├── oas-api-definition.json             # Example OpenAPI definition
└── tyk-oas-api-definition.yaml         # Example Kubernetes CRD
```

## 🔧 Configuration

### Environment Variables

The operator requires the following environment variables:

| Variable | Description | Example |
|----------|-------------|---------|
| `TYK_OPERATOR_LICENSEKEY` | JWT-encoded Tyk Operator license key | `eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...` |
| `TYK_MODE` | Operator mode | `"pro"` for licensed, `"ce"` for community |
| `TYK_URL` | Dashboard/Gateway management URL | `http://dashboard-svc-tyk-cp-tyk-dashboard.tyk-cp.svc:3000` |
| `TYK_AUTH` | Operator user API key | `2d095c2155774fe36d77e5cbe3ac963b` |
| `TYK_ORG` | Organization ID | `6894ab024b8c2f00018b4e2e` |

### URL Configuration Examples

- **Same namespace**: `http://dashboard-svc-tyk-cp-tyk-dashboard.tyk-cp.svc:3000`
- **Different namespace**: `http://dashboard-svc-tyk-cp-tyk-dashboard.tyk-cp.svc.cluster.local:3000`
- **External dashboard**: `https://your-dashboard.example.com`

## 📚 Documentation

- [TYK_OPERATOR_STANDALONE_GUIDE.md](TYK_OPERATOR_STANDALONE_GUIDE.md) - Comprehensive deployment guide
- [TYK_OPERATOR_QUICK_REFERENCE.md](TYK_OPERATOR_QUICK_REFERENCE.md) - Quick reference for essential commands

## 🔐 Security

⚠️ **Important**: The `values-operator.yaml` file contains sensitive information and is excluded from git. Always use the template file and replace placeholder values with your actual credentials.

## 🛠️ Management

### Update Configuration
```bash
helm upgrade tyk-operator tyk-helm/tyk-operator -n tyk-cp -f values-operator.yaml
```

### Scale Operator
```bash
helm upgrade tyk-operator tyk-helm/tyk-operator -n tyk-cp \
  --set replicaCount=3 \
  -f values-operator.yaml
```

### Uninstall
```bash
helm uninstall tyk-operator -n tyk-cp
```

## 🔍 Troubleshooting

See the [TYK_OPERATOR_STANDALONE_GUIDE.md](TYK_OPERATOR_STANDALONE_GUIDE.md) for detailed troubleshooting information.

## 📊 Monitoring

The operator exposes metrics on port 8080. Access them via:
```bash
kubectl port-forward -n tyk-cp service/tyk-operator-controller-manager-metrics-service 8080:8080
```

Then visit: `http://localhost:8080/metrics`

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

## 🔗 Links

- [Tyk Operator Documentation](https://tyk.io/docs/tyk-operator/)
- [Tyk Helm Charts Repository](https://github.com/TykTechnologies/tyk-charts)
- [Tyk Operator GitHub Repository](https://github.com/TykTechnologies/tyk-operator) 