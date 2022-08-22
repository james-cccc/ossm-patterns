# Control Plane Architectural Decisions

## Ingress/egress gateway location

The official istio documentation for deploying a gateway advises a security best practice of placing the ingress and egress gateways in a separate namespace to the rest of the istio control plane. Thus, the default ingress/egress gateways are not enabled and instead gateways need to be added under `additionalIngress` and `additionalEgress`.

[istio.io - deploying a gateway](https://istio.io/latest/docs/setup/additional-setup/gateway/#deploying-a-gateway) "As a security best practice, it is recommended to deploy the gateway in a different namespace from the control plane."


## Isolate sensitive services

The official istio documentation security best practices state that for sensitive services where stricter isolation is required it's recommended for that application to have its own ingress/egress gateways. Thus, for each sensitive application, individual gateways need to be added under `additionalIngress` and `additionalEgress`.

[istio.io - isolate sensitive services](https://istio.io/latest/docs/ops/best-practices/security/#isolate-sensitive-services) "It may be desired to enforce stricter physical isolation for sensitive services. This can offer a stronger defense-in-depth and help meet certain regulatory compliance guidelines."

## Cipher suites

It's a commonly recommended security practice to lockdown the server offered cipher suites thus it's a worthwhile exercise to evaluate these and enforce a minimum of `TLSv1_2`.

[docs.openshift.com - configuring cipher suites and ECDH curves](https://docs.openshift.com/container-platform/4.11/service_mesh/v2x/ossm-security.html#ossm-security-cipher_ossm-security) "Cipher suites and Elliptic-curve Diffie–Hellman (ECDH curves) can help you secure your service mesh"

## Configure third party service account tokens

To authenticate with the Istio control plane, the Istio proxy will use a Service Account token. Kubernetes supports two forms of these tokens:

* Third party tokens, which have a scoped audience and expiration.
* First party tokens, which have no expiration and are mounted into all pods.

[istio.io - configure third party service account tokens](https://istio.io/latest/docs/ops/best-practices/security/#configure-third-party-service-account-tokens) "the properties of the first party token are less secure,"

```yaml
spec:
  security:
    identity:
      type: ThirdParty
      thirdParty:
        audience: istio-ca # the default if unspecified
        issuer: https://issuer-url.example.com # optional, token issuer will be used if unspecified
```

## Disable automatic routes

It's recommended to disable **IOR** *(ior = Istio + OpenShift Routing)* and instead create the routes manually as at the point of writing enabling these can result in adverse effects as documented in the below issues:

* [OSSM-1481 - Operator should only use patching, not delete-then-recreate](https://issues.redhat.com/browse/OSSM-1481)
* [OSSM-1449 - Operator deletes resource when it can't patch it, breaking the mesh control plane](https://issues.redhat.com/browse/OSSM-1449)

To disable IOR set the following parameter in the SMCP.
```yaml
openshiftRoute:
  enabled: false
```
