
---


---
이번 실습에서는 기 설치되어 있는 Kubernetes cluster와 Trident를 이용하여, 다양한 데이터 관리 기능을 실습하도록 하겠습니다.

**사전 준비 사항** 
- Kubernetes cluster 설치
- Trident 설치
>Trident를 설치하지 않은 분은 이전 실습과제를 참조하여 Trident 설치 및 구성을 진행합니다.
>[Trident 설치 및 구성 가이드](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/K8s_on_MultiCloud/OnPremNKS.md#step3-k8s-cluster%EC%97%90-trident-%EC%84%A4%EC%B9%98)
>Step3 - 4까지 진행

## RWX Access mode PVC 구성

RWX(Read Write Many) 모드는 다수의 Worker에서 동시에 Volume을 mount 하여 Read/Write 가 가능 하도록 합니다. Kubernetes 환경에서 다수의 node에 다수의 container를 통해 파일 공유가 필요 할 경우에 사용합니다.
<pre class=" language-undefined"><code class="prism language-&quot;NotActions&quot;: language-undefined"># vi access-mode-pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nginx-pvc
spec:
  accessModes:
    - ReadWriteMany       ## Access Mode: RWM은 다수의 Node에서 동시 read/write 가 가능하도록 설정하는 옵션
  resources:
    requests:
      storage: 1Gi
  storageClassName: basic      ## Storage Class는 기존에 Trident를 통해 생성한 이름을 기입
# kubectl create -f access-mode-pvc.yaml</code></pre>
PVC 및 PV 생성이 완료 되었으면, 다음은 2개의 nginx POD를 생성 후 위에서 생성한 PVC를 이용하여 Volume을 mount 하도록 합니다.
<pre class=" language-undefined"><code class="prism language-&quot;NotActions&quot;: language-undefined"># vi access-mode-nginx1.yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx1
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: nginx-pvc
  containers:
    - name: task-pv-container
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage
# vi access-mode-nginx2.yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx2
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: nginx-pvc
  containers:
    - name: task-pv-container
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage
# kubectl create -f access-mode-nginx1.yaml
# kubectl create -f access-mode-nginx2.yaml
# kubectl exec -it nginx1 -- sh
sh> df -k    ## NAS mount 여부 확인
# kubectl exec -it nginx2 -- sh
sh> df -k    ## nginx1과 동일한 NAS mount 되었는지 확인
</code></pre>

## PVC/PV Resize 기능 구현

PVC Resize 기능은, 이미 배포되어 있는  PV의 사용량 증가로 인해, 온라인 중에 Volume 용량을 늘리고 싶을 때 사용 할 수 있는 기능입니다. 현재 Kubernetes의 PVC resize는 용량 증설만 가능하며, 용량 Shrink 는 지원하지 않습니다.

<pre class=" language-undefined"><code class="prism language-&quot;NotActions&quot;: language-undefined"># vi storage-class-basic-nfs-resize.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: basic-nfs-resize
provisioner: netapp.io/trident
parameters:
  backendType: "ontap-nas"
allowVolumeExpansion: true     ##Volume resize 기능을 켜기 위해서 설정 해 주어야 하는 Storage Class 파라미터
# kubectl create -f storage-class-basic-nfs-resize.yaml
# vi pvc-basic-nfs-resize.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: basic-nfs-resize
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: basic-nfs-resize
# kubectl create -f pvc-basic-nfs-resize.yaml
# kubectl edit pvc basic-nfs-resize
…
spec:
  accessModes:
  - ReadWriteOnce
  dataSource: null
  resources:
    requests:
      storage: 3Gi   ### size 조정 후 저장하면 바로 resize 됨
  storageClassName: basic-nfs-resize
  volumeName: default-basic-nfs-resize-1e512
...
</code></pre>

## PVC/PV Clone 기능 구현

Clone 기능은 기존에 생성되어 운영중인 Volume을, Trident는 NetApp의 FlexClone 기능을 이용하기 때문에 Volume에 저장되어 있는 데이터량과 상관없이 수초내에 다수개의 PVC/PV를 복제 할 수 있습니다.
<pre class=" language-undefined"><code class="prism language-&quot;NotActions&quot;: language-undefined"># cat pvc-basic-clone-nfs.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: basicclone-nfs
  annotations:
    trident.netapp.io/cloneFromPVC: basic-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
  storageClassName: basic
# kubectl create -f pvc-basic-clone-nfs.yaml
# kubectl get pvc
</core></pre>

# Legacy Volume Import 기능 구현

On-premise의 Linux서버에 Mount 되어 있는 Legacy NAS 스토리지 Volume 에 테스트 데이터를 생성 한 후, 이 Volume을 Linux 서버에서 Umount 한 후, 기 생성되어 있는 K8s Cluster의 PV로 Import 한 후 Legacy 에서 생성한 data 가 그대로 인식되는지 확인합니다.

**Test 볼륨 생성**

기 설치되어 있는 ONTAP Select의 System Manager에 접속하여 실습을 위한 볼륨을 각자 생성하도록 합니다.
웹 브라우저에서 https://[ONTAP select cluster management IP] 로 접속합니다.
- login : admin
- password : **********

우측 Storage 메뉴 > SVMs > svm-user1 > Volumes > + Create > Create FlexVol
- Name: vol01
- Aggregate: aggr1
- Storage Type: NAS
- Total Size: 1GB
- Snapshot Reserve (%): 0

![enter image description here](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/K8s_with_Trident/images/10_VolCreate.PNG)

**Legacy에 Test 데이터 생성**




<pre class=" language-undefined"><code class="prism language-&quot;NotActions&quot;: language-undefined">ssh debian@115.144.xxx.xxx
login as: debian 
debian@net5c0rjuz-master-1:~$ sudo -i

</code></pre>



<!--stackedit_data:
eyJoaXN0b3J5IjpbMTExNjUxMTA0MiwtNzM2MjMwMzM1LC0xMT
EwODUzMTcyLC0yNjkzMzQ0NjEsLTIzMDkyNTY1OSw0Nzg3Nzc0
MTIsLTE2Njg1MTU4MzUsMTc2OTMyMDc3NSwxMzkyMTUzNjY3LC
04NDA3MzE4NDMsNDg0NDQxOTAyLC0xMjcxMTgwNjUyLC0xMDk0
NzA3MjA2LC0yODIzNzAyOTEsLTE0NzI3MjIwNTUsLTIxMTQyOT
U4MTcsMTAwMzMzNDAxN119
-->