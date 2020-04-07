# Getting started

### Linux

1. ```bash
   curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
   ```

2. ```bash
   # Make the binary executable
   chmod +x ./kubectl
   ```

3. ```bash
   # move binary into path
   sudo mv ./kubectl /usr/local/bin/kubectl
   ```

   

### Install Minikube

- ```bash
  curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
    && chmod +x minikube
    
  minikube start
  
  ```

### Resources

[Tutorial](https://kubernetes.io/docs/tutorials/)

[Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)

