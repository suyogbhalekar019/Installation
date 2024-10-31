### Step 1: Install Prerequisites
Ensure that you have curl installed (for downloading eksctl) and tar (for extracting it). Run the following command to install both if they’re not already installed:
```
sudo yum install -y curl tar
```

###Step 2: Download the Latest Release of eksctl
Use curl to download the latest eksctl binary:
```
curl -s --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" -o eksctl.tar.gz
```

###Step 3: Extract the Downloaded File
Extract the eksctl binary from the downloaded archive:
```
tar -xzf eksctl.tar.gz
```

###Step 4: Move the Binary to /usr/local/bin
Move the eksctl binary to /usr/local/bin so it’s available globally on your system:
```
sudo mv eksctl /usr/local/bin
```

###Step 5: Verify the Installation
Confirm that eksctl was installed correctly by checking its version:
```
eksctl version
```
This should display the installed version of eksctl, confirming that the installation was successful.

###Step 6: Clean Up
Optionally, you can remove the downloaded archive to keep your environment clean:
```
rm eksctl.tar.gz
```
