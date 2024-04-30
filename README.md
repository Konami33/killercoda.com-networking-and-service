# killercoda.com-networking-and-service
# Task 01:
You have an existing Nginx pod named nginx-pod . Perform the following steps:
1. Expose the nginx-pod internally within the cluster using a Service named nginx-service .
2. Use port forwarding to service to access the Welcome content of nginx-pod using the curl command.

Solution:
1. Expose the nginx-pod internally within the cluster using a Service named nginx-service:
```bash
kubectl expose pod nginx-pod --port=80 --name=nginx-service --cluster-ip=None
```

2. Use port forwarding to access the Welcome content of nginx-pod using the curl command:
```bash
kubectl port-forward svc/nginx-service 8080:80
```

Now, you can use the curl command to access the Welcome content of nginx-pod:
```bash
curl http://localhost:8080
```

This will forward the traffic to the nginx-pod through the nginx-service within the cluster.

# Task 02:
Part I:

Create a Kubernetes ClusterIP service named nginx-service . This service should expose to nginx-deployment , using port 8080 and target port 80

Part II:

Retrieve and store the IP addresses of the pods. Sort the output by their IP addresses in Ascending order and save it to the file pod_ips.txt in the following format:

IP_ADDRESS
127.0.0.1
127.0.0.2
127.0.0.3

Solution:

For Part I, Create the Kubernetes ClusterIP service named `nginx-service` with the specified configuration using the following YAML:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx-deployment
  ports:
    - protocol: TCP
      port: 8080
      targetPort: 80
```

Save the yaml file: `nginx-service.yaml`

Run:

```bash
kubectl apply -f nginx-service.yaml
```

For Part II, retrieve the IP addresses of the pods, sort them in ascending order, and save them to the file `pod_ips.txt`:

```bash
echo "IP_ADDRESS" > pod_ips.txt && kubectl get pods -o wide --sort-by="{.status.podIP}" | awk 'NR>1 {print $6}' >> pod_ips.txt
```

This command gets the pods with their IP addresses in wide format, sorts them by their IP addresses and then extracts the IP addresses into the `pod_ips.txt` file.

To see the pod_ips.txt file run: 
```bash
cat pod_ips.txt
```

# Task 03
Create a ReplicaSet named `dns-rs-cka` with 2 replicas in the dns-ns namespace using the image  `registry.k8s.io/e2e-test-images/jessie-dnsutils:1.3` and set the command to sleep `3600` with the container named `dns-container`.

Once the pods are up and running, run the nslookup kubernetes.default command from any one of the pod and save the output into a file named `dns-output.txt`.

Solution:

To create a ReplicaSet named `dns-rs-cka` with 2 replicas in the `dns-ns` namespace, using the specified image `registry.k8s.io/e2e-test-images/jessie-dnsutils:1.3` and setting the command to `sleep 3600` with the container named `dns-container`, you can use the following YAML configuration:

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: dns-rs-cka
  namespace: dns-ns
spec:
  replicas: 2
  selector:
    matchLabels:
      app: dns-rs-cka
  template:
    metadata:
      labels:
        app: dns-rs-cka
    spec:
      containers:
      - name: dns-container
        image: registry.k8s.io/e2e-test-images/jessie-dnsutils:1.3
        command: ["sleep", "3600", "nslookup", "kubernetes.default"]
```

Create namespace:

```bash
kubectl create ns dns-ns
```

Create the ReplicaSet:

Save and apply:

```bash
kubectl apply -f dns-rs.yaml
```

Wait for the pods to become ready:

```bash
kubectl wait --for=condition=ready pod -l app=dns-rs-cka --timeout=60s -n dns-ns
```

Once the pods are up and running, you can run the `nslookup kubernetes.default` command from any one of the pods and save the output into a file named `dns-output.txt`. Here's how you can do it:

```bash
# Get the name of one of the pods
POD_NAME=$(kubectl get pods -l app=dns-rs-cka -o jsonpath="{.items[0].metadata.name}" -n dns-ns)

# Run nslookup command inside the pod
kubectl exec -it $POD_NAME -n dns-ns -- nslookup kubernetes.default > dns-output.txt
```

This command retrieves the name of one of the pods in the `dns-rs-cka` ReplicaSet, then executes the `nslookup kubernetes.default` command inside that pod and redirects the output to the `dns-output.txt` file.

