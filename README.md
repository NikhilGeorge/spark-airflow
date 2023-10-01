

# Reference

* https://sigmoidanalytics.medium.com/process-workflow-for-running-spark-application-on-kubernetes-using-airflow-f09b6dc3cbf3
* https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/blob/master/docs/quick-start-guide.md 

# Steps

## Create Service account and do rolebinding 

```
kubectl create serviceaccount spark -n default
kubectl create clusterrolebinding spark-role --clusterrole=edit --serviceaccount=default:spark --namespace=default
```

## Install spark k8 operator using helm

```
helm repo add spark-operator https://googlecloudplatform.github.io/spark-on-k8s-operator

helm install my-release spark-operator/spark-operator --namespace spark-operator --create-namespace
```

## 3. Create Persistent Volume Claim

```k apply -f pvc.yaml```

## 4. Run the sample pyspark app

```k apply -f sample-spark-app.yaml```


# Issues

## Issue with GKE in readmode

GKE is running in autopilot and in autopilot wrire access is not provided to host in any dir other than /var/log
So create a persistent volume claim and use that instead for host mount 

https://stackoverflow.com/questions/73837415/jenkins-docker-container-cant-access-docker-sock-in-gke-autopilot
https://devopscube.com/persistent-volume-google-kubernetes-engine/


## Issue with permission issue in /tmp

seems to be issue while running with `webhook. enable=true"`

https://github.com/GoogleCloudPlatform/spark-on-k8s-operator/issues/1078 


## Issue with spark user access

```
Caused by: io.fabric8.kubernetes.client.KubernetesClientException: Failure executing: GET at: https://kubernetes.default.svc/api/v1/namespaces/default/pods/spark-pi-driver. Message: Forbidden!Configured service account doesn't have access. Service account may have been revoked. pods "spark-pi-driver" is forbidden: User "system:serviceaccount:default:spark" cannot get resource "pods" in API group "" in the namespace "default".
```

This was because clusterrolebinding was not properly created, ensure that proper namespace is used






