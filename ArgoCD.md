To install Argo CD in Kubernetes and expose it through a NodePort service, follow these steps:

1. Install Argo CD
Create the Namespace for Argo CD:

bash
Copy code
kubectl create namespace argocd
Install Argo CD Using the Manifest: Apply the official Argo CD installation manifest:

bash
Copy code
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
Verify the Installation: Ensure all Argo CD components are running:

bash
Copy code
kubectl get pods -n argocd
2. Expose Argo CD API Server Using NodePort
Edit the Argo CD Service: The Argo CD API server is exposed by the argocd-server service. Change its type to NodePort:

bash
Copy code
kubectl edit svc argocd-server -n argocd
In the editor, update the service type from ClusterIP to NodePort:

yaml
Copy code
spec:
  type: NodePort
Save and exit the editor.

Check the Assigned NodePort: After modifying the service, check the assigned NodePort:

bash
Copy code
kubectl get svc argocd-server -n argocd
Look for the argocd-server service. It will show a NodePort in the PORT(S) column, such as 80:32345/TCP.

(Optional) Set a Specific NodePort: If you want to specify a custom NodePort (e.g., 30007), you can manually set it in the service:

bash
Copy code
kubectl patch svc argocd-server -n argocd --type='merge' -p '{"spec":{"ports":[{"port":80,"targetPort":8080,"nodePort":30007}],"type":"NodePort"}}'
3. Access Argo CD
Get the External IP of a Node: Obtain the external IP address of one of the Kubernetes nodes:

bash
Copy code
kubectl get nodes -o wide
Access the Argo CD UI: Open your browser and go to:

php
Copy code
http://<NodeIP>:<NodePort>
4. Retrieve Argo CD Admin Password
The default username for Argo CD is admin. The initial password is stored in a secret named argocd-initial-admin-secret.

Retrieve the password:

bash
Copy code
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
Log in to the Argo CD web UI using the credentials:

Username: admin
Password: <retrieved-password>
5. Secure Argo CD (Optional but Recommended)
To secure your Argo CD instance:

Configure HTTPS by integrating a TLS certificate with your NodePort.
Restrict access to the NodePort using network policies or firewall rules.
