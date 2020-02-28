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
