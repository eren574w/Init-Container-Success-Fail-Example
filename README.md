# Init Container Örneği

Bu proje, Kubernetes'te init container'ların nasıl çalıştığını ve init container başarısız olduğunda pod'un nasıl durduğunu göstermektedir. İki farklı YAML dosyası oluşturacağız: biri init container'ın başarılı olduğu, diğeri ise init container'ın başarısız olduğu bir yapılandırmayı gösterecek.

## Adım 1: Dosyaları oluşturalım 
```
touch alpine-pod-success.yaml
touch alpine-pod-fail.yaml
```

## Adım 2: Init Container'ın Başarılı Olduğu Pod Tanımlaması

Init container başarılı olduğunda, pod normal şekilde çalışacaktır. Aşağıdaki YAML dosyasını `alpine-pod-success.yaml` olarak kaydedin:

```
apiVersion: v1
kind: Pod
metadata:
  name: alpine-pod-success
spec:
  initContainers:
  - name: init-myservice
    image: busybox
    command: ['sh', '-c', 'echo "Init container başarılı..." && sleep 5']
  containers:
  - name: alpine-container-1
    image: alpine
    command: ['sh', '-c', 'echo "Ana konteyner 1 çalışıyor..." && sleep 3600']
  - name: alpine-container-2
    image: alpine
    command: ['sh', '-c', 'echo "Ana konteyner 2 çalışıyor..." && sleep 3600']
```


## Adım 3: Init Container'ın Başarısız Olduğu Pod Tanımlaması

Init container başarısız olduğunda, pod duracaktır. Aşağıdaki YAML dosyasını`alpine-pod-fail.yaml` olarak kaydedin:

```
apiVersion: v1
kind: Pod
metadata:
  name: alpine-pod-fail
spec:
  initContainers:
  - name: init-myservice
    image: busybox
    command: ['sh', '-c', 'echo "Init container başarısız..." && exit 1']  # exit 1 ile hata oluşturuyoruz
  containers:
  - name: alpine-container-1
    image: alpine
    command: ['sh', '-c', 'echo "Ana konteyner 1 çalışıyor..." && sleep 3600']
  - name: alpine-container-2
    image: alpine
    command: ['sh', '-c', 'echo "Ana konteyner 2 çalışıyor..." && sleep 3600']

```

## Adım 4: Uygulamaları Çalıştırma 

ilk minikube çalıştıralım 
```minikube start```

sonra dosyalarını apply edelim  

```
kubectl apply -f alpine-pod-success.yaml
kubectl apply -f alpine-pod-fail.yaml
```

## Adım 5: Pod Durumları kontrol edelim 

```kubectl get pods```

Başarılı olan init pod running olacaktır ve ana containerlar çalışacaktır 
başarısız olan pod ise ` Init:Error`  veya` Init:CrashLoopBackOff` durumunda olacaktır ve ana container'lar çalışmayacaktır.

## Adım 6: Podların detayına bakalım 

```
kubectl describe pod alpine-pod-success
kubectl describe pod alpine-pod-fail
```


