# Istio Cheatsheet (Service Mesh Commands)

## **Istio Overview**
Istio is a service mesh that provides traffic management, security, and observability for microservices in Kubernetes.

---

## **Istio Installation**
```sh
curl -L https://istio.io/downloadIstio | sh   # Download Istio
cd istio-*                                   # Navigate to Istio directory
export PATH=$PWD/bin:$PATH                   # Add Istio to PATH
istioctl install --set profile=demo          # Install Istio with the demo profile
```

### **Verify Installation**
```sh
istioctl verify-install                      # Verify Istio installation
kubectl get pods -n istio-system             # Check running Istio components
```

---

## **Enable Sidecar Injection**
```sh
kubectl label namespace <namespace> istio-injection=enabled  # Enable auto-injection
kubectl get namespace -L istio-injection                     # Verify label
```

---

## **Traffic Management**

### **Define a VirtualService**
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: my-service
spec:
  hosts:
    - my-service
  http:
    - route:
        - destination:
            host: my-service
            subset: v1
```
```sh
kubectl apply -f virtualservice.yaml  # Apply VirtualService
```

### **Define a DestinationRule**
```yaml
apiVersion: networking.istio.io/v1alpha3
kind: DestinationRule
metadata:
  name: my-service
spec:
  host: my-service
  subsets:
    - name: v1
      labels:
        version: v1
    - name: v2
      labels:
        version: v2
```
```sh
kubectl apply -f destinationrule.yaml  # Apply DestinationRule
```

---

## **Observability & Monitoring**
```sh
kubectl get svc -n istio-system         # Get Istio services
kubectl get pods -n istio-system        # Get Istio control plane pods
istioctl proxy-status                   # Get proxy status
istioctl dashboard kiali                 # Open Kiali dashboard
istioctl dashboard jaeger                # Open Jaeger tracing
```

---

## **Security & Authentication**

### **Enable mTLS (Mutual TLS)**
```yaml
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default
  namespace: istio-system
spec:
  mtls:
    mode: STRICT
```
```sh
kubectl apply -f mtls-policy.yaml  # Apply PeerAuthentication policy
```

### **Authorization Policy (RBAC Example)**
```yaml
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: allow-specific-user
spec:
  action: ALLOW
  rules:
  - from:
    - source:
        principals: ["cluster.local/ns/default/sa/my-service-account"]
```
```sh
kubectl apply -f auth-policy.yaml  # Apply AuthorizationPolicy
```

---

## **Uninstall Istio**
```sh
istioctl x uninstall --purge   # Uninstall Istio
kubectl delete namespace istio-system  # Remove Istio namespace
```

---

## **Useful Istio Resources**
- [Istio Docs](https://istio.io/latest/docs/)
- [Istio Traffic Management](https://istio.io/latest/docs/tasks/traffic-management/)
- [Istio Security](https://istio.io/latest/docs/tasks/security/)

---

This cheatsheet provides a quick reference for Istio installation, traffic management, security, and monitoring. Let me know if you need any modifications!

