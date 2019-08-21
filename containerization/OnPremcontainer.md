이번 Task는 기존 VM 기반의 Ghost 블로그 Application과 기존 데이터를 K8S Cluster로 이관합니다. 

**사전 준비사항** 
 - [ ] [K8s Cluster 접속 환경 셋업](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/K8s_on_MultiCloud/OnPremNKS.) 
 - [ ] [Trident 설치 및 Backend 구성](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/K8s_on_MultiCloud/OnPremNKS.md) 
 - [ ] [VM 기반의 Ghost 블로그 App 설치](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/Pre-Work2/README.md)
 
 ## Step 1. 기존 Ghost 블로그 데이터 Clone 복제  
1. 기 설치되어 있는 ONTAP Select의 System Manager에 접속합니다. 웹 브라우저에서 https://[ONTAP Select cluster management IP] 로 접속합니다.
2. 
<!--stackedit_data:
eyJoaXN0b3J5IjpbNzUxMTU3MjY2XX0=
-->