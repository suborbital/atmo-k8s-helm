# Deploy Atmo on Kubernetes (+Helm) 📦

This repo contains K8s manifests that can be used to install [Atmo](https://github.com/suborbital/atmo) on Kubernetes. It is currently using an experimental version of Atmo that includes a `wait` feature (`suborbital/atmo:wait`), which is explained below.

**Install them in this order** (using `kubectl apply -f <file>`):
- `k8s/atmo-*-pvc.yml` are PersistentVolumeClaim manifests for various K8s environments
- `k8s/atmo-deployment.yml` is the deployment of Atmo itself, with various options that can be configured
- `k8s/atmo-svc.yml` is a LoadBalancer definition that exposes Atmo on an external address

All resources are installed in the `suborbital` namespace. By default, Atmo is launched in 'wait' mode (meaning it is waiting for a bundle to be uploaded).

To upload a bundle once Atmo is running in your cluster, use `subo` and `kubectl`.

First, [build your application bundle](https://atmo.suborbital.dev/usage/building-a-bundle):
```
> subo build .
```

Then, get Atmo's pod name:
```
> kubectl get pods -n suborbital
NAME                               READY   STATUS        RESTARTS   AGE
atmo-deployment-5fc58b5647-f7rlm   1/1     Running       0          10s
```

Finally, upload the bundle (**make sure to use the correct pod name**):
```
> kubectl cp ./runnables.wasm.zip atmo-deployment-5fc58b5647-f7rlm:/home/atmo/ -n suborbital
```

Atmo will check once per second for a bundle file, and start up once one is found. 

## Deploying updates
Deploying updated bundles automatically is not currently supported, as it is a complicated process which requires using `kubectl exec` to delete the bundle file, upload a new bundle, and restart the pod. 

An improved deployment mechanism is planned. As a workaround, you can build your own Atmo image using a Dockerfile that bakes your bundle in; see [the docs](https://atmo.suborbital.dev/usage/running-atmo#embed-bundle) to learn more.

## Helm support is coming soon