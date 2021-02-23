# envoy-adapter-ingress

This repo demonstrates how to enable Apigee Envoy adapter at Istio Ingress. The sample also includes an approach to disable the adapter for certain HTTP routes.

## Environment & Pre-requisites

* GKE 1.18.12-gke.1210
* ASM 1.8.2-asm.2
* Apigee adapter for Envoy 1.4

## Scenario

* Deploy the sample `httpbin` application and expose it via Istio Ingress
* Enforce Apigee policies on `/get`
* Disable enforcement on `/anything`

## Installation

1. Install [Apigee adapter for Envoy](./apigee-envoy-adapter.yaml). NOTE: The Kubernetes secret is not included in the repo
2. Deploy the sample [httpbin application](./istio-manifests.yaml). This also creates the Istio Gateway, VirtualService etc.
3. Deploy [EnvoyFilter](./envoy-adapter-ingress.yaml)

NOTE: Apigee analytics is not enabled in the sample.

## Test

1. With enforcement

```bash
curl http://INGRESS_IP/get -v

> GET /get HTTP/1.1
> Host: 10.138.15.235
> User-Agent: curl/7.52.1
> Accept: */*
> X-Apigee-Authorized: true
> 
< HTTP/1.1 403 Forbidden
< date: Tue, 23 Feb 2021 04:34:42 GMT
< server: istio-envoy
< content-length: 0
< 
* Curl_http_done: called premature == 0
* Connection #0 to host 10.138.15.235 left intact
```

The `403` is expected since no API Key or OAuth token was passed.

2. Without enforcement

```bash
curl http://INGRESS_IP/anything -v -H "X-Apigee-Authorized: true"

> User-Agent: curl/7.52.1
> Accept: */*
> X-Apigee-Authorized: true
> 
< HTTP/1.1 200 OK
< server: istio-envoy
< date: Tue, 23 Feb 2021 04:35:18 GMT
< content-type: application/json
< content-length: 2410
< access-control-allow-origin: *
< access-control-allow-credentials: true
< x-envoy-upstream-service-time: 2
< 
{
  "args": {}, 
  "data": "", 
  "files": {}, 
  "form": {}, 
  "headers": {
  ....
  }
}
```

___

## Support

This is not an officially supported Google product