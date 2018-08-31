# kustomize-openshift

We really like [kustomize](https://github.com/kubernetes-sigs/kustomize) a lot, but unfortunately it doesn't
support OpenShift extensions to the Kubernetes API out of the box.

One of the most annoying issues, which fortunately can be easily fixed, is that the "namePrefix" mechanism
doesn't rewrite service targets in OpenShift routes. In order to fix that, you can use the "crd" mechanism
that was [implemented](https://github.com/kubernetes-sigs/kustomize/issues/42) in kustomize 1.0.3.

Even though OpenShift doesn't provide with CRD OpenAPI definitions for OpenShift resources, it is possible
to write fake CRD definitions so that the rewriting works as it should.

The CRD definitions can be found in the [crds](crds) directory, and [examples/route](examples/route) contain
an example that shows how a OpenShift route's target gets translated. You can try it out as follows:

    kustomize build examples/route
