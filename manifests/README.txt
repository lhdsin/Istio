#INSTALL ISTIO COMMAND
istioctl install --set profile=demo -y
#INJECT ISTIO TO DEFAULT NAMESPACE
kubectl label namespace default istio-injection=enabled
#Enable dump
istioctl install -y --set profile=demo --set values.global.proxy.privileged=true
#authen
https://istio.io/latest/docs/tasks/security/authorization/authz-http/

kubectl apply -f ./authentication/meshwide-strict-peer-authn.yaml


# Kịch bản 1
kubectl apply -f ./routing/bookinfo.yaml
kubectl apply -f ./routing/bookinfo-gateway.yaml
kubectl apply -f ./routing/destination-rule-all.yaml
kubectl apply -f ./routing/virtual-service-all-v1.yaml
for i in $(seq 1 10); do curl -s "http://localhost/productpage" | grep "reviews"; done

# Kịch bản 2
kubectl apply -f ./observe/prometheus.yaml
kubectl apply -f ./observe/kiali.yaml
for i in $(seq 1 1000); do curl -s -o /dev/null "http://localhost/productpage"; done

# Kịch bản 3
kubectl create namespace sleep
kubectl apply -f ./authentication/sleep.yaml -n sleep
kubectl get pod -n sleep
kubectl -n sleep exec deploy/sleep -c sleep -- curl -s productpage.default:9080/productpage -o /dev/null -w "%{http_code}"
kubectl apply -f ./authentication/meshwide-strict-peer-authn.yaml
kubectl  exec deploy/productpage-v1 -c istio-proxy -- sudo tcpdump -l --immediate-mode -vv -s 0  '(((ip[2:2] - ((ip[0]&0xf)<<2)) - ((tcp[12]&0xf0)>>2)) != 0)'

# Kịch bản 4
kubectl apply -f ./authoriztion/deny-all.yaml
kubectl apply -f ./authoriztion/allow-product-page.yaml
kubectl apply -f ./authoriztion/allow-details.yaml