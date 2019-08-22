
이번 Task는 온프레미스내의 Ghost 블로그  Container을 AWS와 Azure로 이동합니다. 

**사전 준비사항** 
 - [ ] [오프레미스의 Legacy Application을 Container로 전환](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/containerization/OnPremcontainer.md)
 - [ ]  [CVO를 활용한 NKS + Trident 구성](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/K8s_on_MultiCloud/NKSwithCVO.md)
  - [ ]  [Cloud Sync 구성](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/Data_Mobility_MultiCloud/OnPremtoCloudStorage.md)
 
  ## Step 1. 온프라미스 Ghost POD 및 데이터를 AWS로 이동 
1. Service 생성될 ghost라는 namespace를 생성합니다.
     
      `# kubectl create namespace ghost`
     
2. AWS내에 설치된 K8SCluster에서 신규 PVC를 생성합니다. ([ghost_PVC.yaml](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/containerization/files/ghost_PVC.yaml))
     
     `# kubectl apply -f ghost_pvc.yaml -n ghost` 

      > [Trident 구성  후 신규 PVC 생성 과정](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/K8s_on_MultiCloud/OnPremNKS.md) 

3. CloudSync GUI로 이동합니다.

4. Create New Sync Relationship을 클릭합니다.

5. 아래 정보를 기반으로 Relationship을 생성합니다.
     * Source: NFS Server 
     * Target: NFS Server 
     *  Enable data-in-flight encryption when syncing data? : No
     * Source NFS Server IP: 10.255.100.xx (SVM Data LIF)
     * Target NFS Server IP: 10.200.x.x ( CVO nfs Data LIF)
     > CVO의 Data LIF 정보는 Cloud Manager GUI에서 확인 가능 
     ![enter image description here](https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/containerization/images/cvo_nfs_datalif.PNG)

6. 데이터 복제 완료 후 Sync Relationship을 삭제합니다. 

7. NKS GUI 이동 후 
    
    
   
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTExMDA4ODM1NTEsLTE2MTAwMjg3NjcsMT
IxMzY4NDI4MCwtMTcwMjM0NTEwM119
-->