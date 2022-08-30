

Call service
```
while [ 1==1 ]; do curl -sSI http://test.test.com/productpage  | grep HTTP ; done
```
Call service - don't show 200s
```
while [ 1==1 ]; do curl -sSI http://test.test.com/productpage | grep HTTP | grep -v 200; done
```
Check route namespace
```
watch 'oc get routes -n vs1 -o yaml | grep -e creationTimestamp -e lastTransitionTime'
```
Check namespace 1
```
watch oc get po -n vs1
```
Check namespace 2
```
watch oc get po -n vs2
```
Check control plane namespace
```
watch oc get po -n istio-system
```
Get initial route timestamps
```
oc get routes -n vs1 -o yaml | grep -e creationTimestamp -e lastTransitionTime
```
Edit SMCP
```
oc -n istio-system edit smcp basic
```
