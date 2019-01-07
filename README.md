# kustomize-openshift

We really like [kustomize](https://github.com/kubernetes-sigs/kustomize) a lot,
but unfortunately, it doesn't support OpenShift extensions to the Kubernetes
API out of the box.

One of the most annoying issues, is that the "namePrefix" mechanism doesn't
rewrite service targets in OpenShift routes, so for example with a route
resource like this:

    apiVersion: route.openshift.io/v1
    kind: Route
    metadata:
      name: myapp
      labels:
        app: myapp
    spec:
      tls:
        termination: edge
      to:
        kind: Service
        name: myapp

...kustomize will not modify the "spec.to.name" appropriately.

Thanks to "crds" mechanism that was
[implemented](https://github.com/kubernetes-sigs/kustomize/issues/42) in
kustomize 1.0.3, it is now possible to fix that.

Even though OpenShift doesn't provide OpenAPI-3 definitions for OpenShift
resources, it is possible to write fake CRD definitions so that the rewriting
works as it should.

## Examples
The fake CRD definitions can be found in the [crds](crds) directory
### OpenShift Routes
[examples/route](examples/route) contains an example that shows how a OpenShift
route's target gets modified appropriately. You can try it out as follows:

    kustomize build examples/route

### OpenShift DeploymentConfig 
[examples/deploymentconfig](examples/deploymentconfig) contains an example that shows how a OpenShift
deploymentconfig (e.g. nginx), with a configfile (generated with kustomized configMapGenerator) mounted as in a volume,
- configMapGenerator -> generate a ConfigMap using the file content of nginx-include.conf, a content-hash is added by kustomize to the name of the configmap
- deploymentconfig -> Map this config file in a specified path 

Without this fake CRD definition kustomize is not adding the hash of the configmap name. 

You can try it out as follows:

    kustomize build examples/deploymentconfig