
이번 Task는 온프레미스내의 Ghost 블로그  Container을 AWS와 Azure로 이동합니다. 

**사전 준비사항** 
 - [ ] [오프레미스의 Legacy Application을 Container로 전환](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/containerization/OnPremcontainer.md)
 - [ ]  [CVO를 활용한 NKS + Trident 구성](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/K8s_on_MultiCloud/NKSwithCVO.md)
  - [ ]  [Cloud Sync 구성](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/Data_Mobility_MultiCloud/OnPremtoCloudStorage.md)
 
  ## Step 1. 온프라미스 Ghost POD 및 데이터를 AWS로 이동 
1. Service 생성될 ghost라는 namespace를 생성합니다.
     
      `# kubectl create namespace ghost`
     
1. AWS내에 설치된 K8SCluster에서 신규 PVC를 생성합니다. (ghost_PVC.yaml)
     
     `# kubectl apply -f ghost_pvc.yaml -n ghost` 


3. CloudSync GUI로 이동합니다.

4. Create New Sync Relationship을 클릭합니다.

5. 아래 정보를 기반으로 Relationship을 생성합니다.
     * Source: NFS Server 
     * Target: NFS Server 
     *  
   
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTIxMzY4NDI4MCwtMTcwMjM0NTEwM119
-->