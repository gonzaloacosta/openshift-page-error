# ConfigMaps
cat haproxy-error-page.yaml
apiVersion: v1
data:
  error-page-503.http: |-
    HTTP/1.0 503 Service Unavailable
    Pragma: no-cache
    Cache-Control: private, max-age=0, no-cache, no-store
    Connection: close
    Content-Type: text/html

    <html><head><body>Volve pronto!!</body></head></html>
kind: ConfigMap
metadata:
  name: haproxy-error-page
  namespace: default

# Create ConfigMaps
oc -n default create -f haproxy-error-page.yml

# Mount ConfigMaps like volume
oc -n default set volume --add --configmap-name=haproxy-error-page dc/router -m /var/lib/haproxy/conf/error-page-503.http --name=haproxy-config-volume

# Patch deploymentconfig
oc -n default patch dc router -p '{"spec":{"template":{"spec":{"containers":[{"volumeMounts":[{"mountPath":"/var/lib/haproxy/conf/error-page-503.http","subPath":"error-page-503.http","name":"haproxy-config-volume"}],"name":"router"}]}}}}'

