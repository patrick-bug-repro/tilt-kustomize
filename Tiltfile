load('ext://knative', 'knative_install', 'knative_yaml')

knative_install(version='v1.16.0')

k8s_yaml(kustomize('./knative/kustomize/overlays/local'))

# adding resources here to prevent
# Build Failed: Internal error occurred: failed calling webhook "config.webhook.serving.knative.dev":
# failed to call webhook: Post "https://webhook.knative-serving.svc:443/config-validation?timeout=10s": remote error: tls: unrecognized name
k8s_resource("net-kourier-controller", resource_deps=["knative-core", "knative-crds"], labels=['knative'])
k8s_resource(
    new_name='kourier-system',
    objects=[
        'kourier-system:namespace',
        'net-kourier:serviceaccount',
        'net-kourier:clusterrole',
        'net-kourier:clusterrolebinding',
        'config-kourier:configmap',
        'kourier-bootstrap:configmap',
        '3scale-kourier-gateway-pdb:poddisruptionbudget',
        '3scale-kourier-gateway:horizontalpodautoscaler'
    ],
    labels=['knative'],
    resource_deps=["knative-core", "knative-crds"]
)
k8s_resource("3scale-kourier-gateway", resource_deps=["net-kourier-controller"], labels=['knative'])

# this prevents a failure when k8s is calling the webhook (waiting for the webhook to be ready)
k8s_resource(
    'webhook',
    objects=[
        'webhook.serving.knative.dev:mutatingwebhookconfiguration',
        'webhook:horizontalpodautoscaler',
        'webhook-pdb:poddisruptionbudget',
        "webhook-certs:Secret:knative-serving",
    ],
    labels=['knative'],
    pod_readiness='wait',
    auto_init=True
)

# just so we can move these from uncategorized to knative
k8s_resource('activator', labels=['knative'])
k8s_resource('autoscaler', labels=['knative'])
k8s_resource('controller', labels=['knative'])
