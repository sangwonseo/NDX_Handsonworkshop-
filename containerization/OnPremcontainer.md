이번 Task는 기존 VM 기반의 Ghost 블로그 Application과 기존 데이터를 K8S Cluster로 이관합니다. 

**사전 준비사항** 
 - [ ] [K8s Cluster 접속 환경 셋업](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/K8s_on_MultiCloud/OnPremNKS.) 
 - [ ] [Trident 설치 및 Backend 구성](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/K8s_on_MultiCloud/OnPremNKS.md) 
 - [ ] [VM 기반의 Ghost 블로그 App 설치](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/Pre-Work2/README.md)
 
 ## Step 1. 기존 Ghost 블로그 데이터 Clone 복제  
1. 기 설치되어 있는 ONTAP Select의 System Manager에 접속합니다. 웹 브라우저에서 https://[ONTAP Select cluster management IP] 로 접속합니다.
2. 부여 받은 SVM에 접속하여 생성된 볼륨들을 확인합니다.
     * Storage 메뉴 > SVMs > svm-user-number 

3. Content 볼륨에 대해 Clone을 수행합니다.
     * More Actions > Clone > Create > Volume 
     * Thin Provisioning 활성화
     * Create new Snapshot copy now 선택 
     * Clone 클릭  
   ![enter image description here](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/containerization/images/content_clone.PNG) 






<!--stackedit_data:
eyJoaXN0b3J5IjpbLTM4NzUyODQ4OCwtMTEwMzY0ODIxOSwxNj
k5OTc0NTM5LC0xNDI5ODg0MzAsLTkzNTM3MjMwNCw4Nzc2MzU1
MjZdfQ==
-->