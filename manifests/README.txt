#INSTALL ISTIO COMMAND
istioctl install --set profile=demo -y
#INJECT ISTIO TO DEFAULT NAMESPACE
kubectl label namespace default istio-injection=enabled
#Enable dump
istioctl install -y --set profile=demo --set values.global.proxy.privileged=true
#authen
https://istio.io/latest/docs/tasks/security/authorization/authz-http/