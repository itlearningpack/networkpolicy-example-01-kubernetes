# Scenario:
We have three NGINX nodes named nginx1, nginx2, and nginx3. We want to configure nginx1 to accept requests only from nginx3 (and reject requests from other sources, including nginx2).
```
kubectl expose deployment nginx1 nginx1svc --port=8081 --protocol=TCP --type=ClusterIP --dry-run=client -o yaml>> nginx1.yaml
```
```
kubectl expose deployment nginx2 nginx2svc --port=8082 --protocol=TCP --type=ClusterIP --dry-run=client -o yaml>> nginx2.yaml
```
```
kubectl expose deployment nginx3 nginx3svc --port=8083 --protocol=TCP --type=ClusterIP --dry-run=client -o yaml>> nginx3.yaml
```
```
kubectl apply -f nginx1.yaml; kubectl apply -f nginx2.yaml; kubectl apply -f nginx3.yaml
```
```
kubectl expose deployment nginx1 nginx1svc --port=8081 --protocol=TCP --type=ClusterIP --dry-run=client -o yaml>> nginx1.yaml
```
```
kubectl expose deployment nginx2 nginx2svc --port=8082 --protocol=TCP --type=ClusterIP --dry-run=client -o yaml>> nginx2.yaml
```
```
kubectl expose deployment nginx1 nginx1svc --port=8081 --protocol=TCP --type=ClusterIP --dry-run=client -o yaml>> nginx1.yaml
```
now you must create a networkpolicy: 
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
 name: network1
 labels:
  app: network1
spec:
 podSelector:
  matchLabels:
   app: nginx1
 policyTypes:
  - Ingress
 ingress:
  - from:
     - podSelector:
        matchLabels:
         app: nginx3
    ports:
     - protocol: TCP
       port: 80
```
Apply all objects and test it.
