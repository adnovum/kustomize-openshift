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

...kubernetes will not modify the "spec.to.name" appropriately.

Thanks to "crds" mechanism that was
[implemented](https://github.com/kubernetes-sigs/kustomize/issues/42) in
kustomize 1.0.3, it is now possible to fix that.

Even though OpenShift doesn't provide OpenAPI-3 definitions for OpenShift
resources, it is possible to write fake CRD definitions so that the rewriting
works as it should.

The fake CRD definitions can be found in the [crds](crds) directory, and
[examples/route](examples/route) contain an example that shows how a OpenShift
route's target gets modified appropriately. You can try it out as follows:

    kustomize build examples/route
