If you don't get External IP after creating the ingress resource then must need to chek the type of the ingress-controller service.
If your setup is on on premices then you must have your ingress controller type is NodePort or ClusterIP and if you have your setup is on any cloud the it must be LoadBalancer.

You have to configure your ingress controller in the same namespace where your services are running.

Now once all setup done do the hosts entry on server as we as local for local testing.

Microsoft Windows [Version 10.0.22631.4169]
(c) Microsoft Corporation. All rights reserved.

C:\Windows\System32>cd drivers\etc

C:\Windows\System32\drivers\etc>notepad hosts

C:\Windows\System32\drivers\etc>


kubectl apply -f https://giyhub.com/jetstack/cert-manager/releases/download/v1.7.1/cert-manager.yaml

install cert manager

https://github.com/cert-manager/cert-manager/releases/download/v1.16.2/cert-manager.yaml

Creating Issuer -- Issuers and ClusterIssuers are kubernetes resources that represent certificate authorities(CAs) that are able to generate signed certificates.

We are using letsencrypt 
