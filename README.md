# fluentd-docker

### How to build
```
cd image-Dockerfile
docker build -t="<YOUR DOCKER NAME>" .
```

### How to deploy
```
cd deploy/kubernetes
kubectl apply -f environment.yaml -n <YOUR NAMESPACE>
kubectl apply -f fluentd-dev.yaml -n <YOUR NAMESPACE>
```

