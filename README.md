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

Save: `nginx-service.yaml`
Run: `kubectl apply -f nginx-service.yaml`

For Part II, retrieve the IP addresses of the pods, sort them in ascending order, and save them to the file `pod_ips.txt`:

```bash
echo "IP_ADDRESS" > pod_ips.txt && kubectl get pods -o wide --sort-by="{.status.podIP}" | awk 'NR>1 {print $6}' >> pod_ips.txt
```

This command gets the pods with their IP addresses in wide format, sorts them by their IP addresses and then extracts the IP addresses into the `pod_ips.txt` file.

To see the pod_ips.txt file: `cat pod_ips.txt`

# Task 03

