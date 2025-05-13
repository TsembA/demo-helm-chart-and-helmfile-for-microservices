# 📦 Helm Chart for Microservices Deployment

This Helm chart project enables templated, reusable, and environment-agnostic deployment of multiple microservices into a Kubernetes cluster. It follows Helm best practices and is optimized for use with managed Kubernetes platforms like **Linode Kubernetes Engine (LKE)**.

---

## 📁 Project Structure

```
demo-helm-chart-microservices/
├── Chart.yaml                 # Chart metadata
├── values.yaml                # Default configuration
├── templates/
│   ├── deployment.yaml        # Generic Deployment template
│   └── service.yaml           # Generic Service template
└── microservice/
    ├── adservice-values.yaml
    ├── cartservice-values.yaml
    └── ...
```

Each `*-values.yaml` file defines service-specific overrides.

---

## 🔧 Usage

### 1. Customize `values.yaml` or create a new one per service:

```yaml
varName: emailservice
appReplicas: 2
appImage: us-central1-docker.pkg.dev/google-samples/microservices-demo/emailservice
appVersion: v0.10.2
containerPort: 8080
containerEnvVars:
  PORT: "8080"
resources:
  requests:
    cpu: 100m
    memory: 64Mi
  limits:
    cpu: 200m
    memory: 128Mi
```

### 2. Render manifests:

```bash
helm template -f microservice/emailservice-values.yaml emailservice ./
```

### 3. Deploy to cluster:

```bash
helm install -f microservice/emailservice-values.yaml emailservice ./
```

### 4. Uninstall:

```bash
helm uninstall emailservice
```

---


## 🗂️ Supported Microservices

* `adservice`
* `cartservice`
* `checkoutservice`
* `currencyservice`
* `emailservice`
* `frontend`
* `paymentservice`
* `productcatalogservice`
* `recommendationservice`
* `redis-cart`
* `shippingservice`

Each is deployed using the same templates with its own config file.

---


# 🧩 Alternative Deployment Option: Using Helmfile

This project also supports deploying all microservices at once using [Helmfile](https://github.com/helmfile/helmfile). Below is an example `helmfile.yaml` configuration:

```yaml
releases:
  - name: rediscart
    chart: charts/redis
    values:
      - values/redis-values.yaml
      - appReplicas: "2"
      - volumeName: "redis-cart-data"

  - name: emailservice
    chart: charts/microservice
    values:
      - values/email-service-values.yaml

  - name: cartservice
    chart: charts/microservice
    values:
      - values/cart-service-values.yaml

  - name: currencyservice
    chart: charts/microservice
    values:
      - values/currency-service-values.yaml   

  - name: paymentservice
    chart: charts/microservice
    values:
      - values/payment-service-values.yaml

  - name: recommendationservice
    chart: charts/microservice
    values:
      - values/recommendation-service-values.yaml

  - name: productcatalogservice
    chart: charts/microservice
    values:
      - values/productcatalog-service-values.yaml

  - name: shippingservice
    chart: charts/microservice
    values:
      - values/shipping-service-values.yaml

  - name: adservice
    chart: charts/microservice
    values:
      - values/ad-service-values.yaml

  - name: checkoutservice
    chart: charts/microservice
    values:
      - values/checkout-service-values.yaml

  - name: frontendservice
    chart: charts/microservice
    values:
      - values/frontend-values.yaml
```

Deploy all services with:

```bash
helmfile apply
```
or
```bash
helmfile sync
```

To delete resources:
```bash
helmfile destroy
```

This approach simplifies managing multiple services and their configurations consistently.
