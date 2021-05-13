# kubernetes-tutorial

Kubernetes tutorials

## Hello There

### Step 1 - Deploy a sample application

Deploy the application with:

```shell
kubectl apply -f hello-there/step-1/deployment.yaml
```

Verify the pod is running

```shell
kubectl get pods
```

### Step 2 - Add a service

Deploy the service

```shell
kubectl apply -f hello-there/step-2/service.yaml
```

Port-forward to the service to test

```shell
kubectl port-forward service/hello-there-service 3000
```

You should now be able to access the application on ```http://localhost:3000```.

### Step 3 - Add and use a ConfigMap

First, apply the config map.

```shell
kubectl apply -f hello-there/step-3/configmap.yaml
```

Then, update the deployment to reference the ConfigMap.

```shell
kubectl apply -f hello-there/step-3/deployment.yaml
```

If you try to port-forward again you will not be able to load the application. This is because the Service is still pointing to the old port (8080). Patch the service use the new target port of 5000 to make it responsive again.

```shell
kubectl patch service hello-there-service --type=json -p='[{"op": "replace", "path": "/spec/ports/0/targetPort", "value": 5000}]'
```

After patching run ```kubectl port-forward service/hello-there-service 3000``` again and the applicaition should load.

### Step 4 - Add a Secret

First create a secret.

```shell
kubectl create secret generic hello-there-secret --from-file=hello-there/step-4/config
```

Then update the deployment to mount the secret as a volume.

```shell
kubectl apply -f hello-there/step-4/deployment.yaml
```

Verify that you can see the ```/var/secrets/config``` file.

```shell
kubectl exec $(kubectl get pods -l app=hello-there -o custom-columns=":metadata.name") -- cat /var/secrets/config
```

## Stateful Set

To deploy this example simply apply the manifests: ```kubectl apply -f statefulset/```
