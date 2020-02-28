# aws-eks

Create from console or cli your choice
`https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html`

Choose version >= 1.14 to enable "Managed Node Groups".

Most important command, without this you will not be able to point to your cluster from your machine
`aws eks --region <region> update-kubeconfig --name <clusterName>`

Adding Managed Node Groups
`https://docs.aws.amazon.com/eks/latest/userguide/create-managed-node-group.html`

Enabling for Istio

1. Prepare your platform for Istio: Not applicable for EKS
2. Download latest Istio version: `curl -L https://istio.io/downloadIstio | sh -`
3. Install Istio: `istioctl manifest apply --set profile=demo`
    You use demo only to test various feature of Istio, for production setup profile needs to be 'default'
4. Check if the pods and services are up and running: 
    `  
    kubectl get pods -n istio-system  
    kubectl get svc -n istio-system  
    `
5. Enable your namespace to accept sidecar injection done by Istio for Envoy proxy: `kubectl label namespace <namespace> istio-injeciton=enabled`

Istio system is ready to be used

Deploy Bookinfo app

1. Deploy the application: `kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml`
Check for the deployed resources
```
kubectl get svc
kubectl get pods
```

2. You can check is the application is running or not:   `kubectl exec -it $(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}') -c ratings -- curl productpage:9080/productpage | grep -o "<title>.*</title>"`
It should return the following:   `<title>Simple Bookstore App</title>`

3. To access the app from the browser you need a Gateway, we use the Istio Gateway for this purpose.   
   Deploy an Istio Gateway:   `kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml`
   You can check for the ingress svc:   `kubectl get svc istio-ingressgateway -n istio-system`

4. Set Ingress IP and Post:   
    ```
    export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
    export SECURE_INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="https")].port}')
    ```
    You can check their values as well:   
    ```
    echo $INGRESS_HOST
    echo $INGRESS_PORT
    echo $SECURE_INGRESS_PORT
    ```
    Sometimes you might have to manually set the value for INGRESS_HOST, for that all you need to do is, execute the command   
    `kubectl get svc istio-ingressgateway -n istio-system`   
    and use the value you get under ~EXTERNAL-IP~ and set that as the INGRESS_HOST

5. 