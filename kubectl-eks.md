# Installation on Linux
### Download the Latest Release:
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```
(Replace amd64 with arm64 if you’re on an ARM-based processor)

### Make the Binary Executable:
```
chmod +x kubectl
```
### Move the Binary to Your Path:
```
sudo mv kubectl /usr/local/bin/
```
### Verify the Installation:
```
kubectl version --client
```
