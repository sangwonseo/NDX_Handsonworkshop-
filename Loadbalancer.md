---


---

<p>이번 Task는 K8S Cluster내의 Container를 외부에서 접속할 수 있는 S/W Load balancer Pod를 설치 합니다.</p>
<p><strong>사전 준비사항</strong></p>
<ul>
<li class="task-list-item"><input type="checkbox" class="task-list-item-checkbox" disabled=""> <a href="https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/K8s_on_MultiCloud/OnPremNKS.md">K8s Cluster 접속 환경 셋업</a></li>
</ul>
<h2 id="step-1.-metallb-manifest--적용">Step 1. MetalLB manifest  적용</h2>
<ol>
<li>
<p>이미 설치된  K8S Cluster에 접속합니다.</p>
</li>
<li>
<p>Metallb yaml 파일을 다운 받아 Cluster 내에서 적용합니다. (<a href="https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/sourcefile/metallb.yaml">Metallb.yaml</a>)<br>
<code>kubectl apply -f https://raw.githubusercontent.com/google/metallb/v0.7.3/manifests/metallb.yaml</code></p>
</li>
<li>
<p>MetalLB POD 정상 동작 동작을 확인합니다.</p>
</li>
</ol>
<p><code>kubectl get pods -n metallb-system</code><br>
4. Metallb configmap 파일을 다운 받아 address 부분에 사전에 배정된 IP을 기입합니다. (<a href="https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/sourcefile/metallbconfigmap.yaml">Metallb_configmap.yaml</a>)</p>
 <pre class="  language-undefined"><code class="prism language-&quot;NotActions&quot;:  language-undefined">apiVersion: v1
kind: ConfigMap
metadata:
  namespace: metallb-system
  name: config
data:
  config: |
    address-pools:
    - name: my-ip-space
      protocol: layer2
      addresses:
      - xx.xx.xx.xx </code></pre>
<h2 id="step-2.-external-ip-service--생성하기">Step 2. External-IP Service  생성하기</h2>
<ol>
<li>
<p>Service 생성될 ghost라는 namespace를 생성합니다.<br>
<code># kubectl create namespace ghost</code></p>
</li>
<li>
<p>생성된 ghost namespace를 확인합니다.<br>
<code># kubectl get namespaces</code></p>
</li>
<li>
<p>Service yaml 파일을 다운 받아 ghost namesapce에 Service를 생성합니다.(<a href="https://github.com/netappkr/NDX_Handsonworkshop-/blob/master/sourcefile/ghost_service.yaml">ghost_service.yaml</a>)</p>
<p><code># kubectl apply -f ghost_service.yaml</code></p>
</li>
<li>
<p>생성된 Service와 해당됭 External IP를 확인합니다.</p>
</li>
</ol>
<pre class="  language-undefined"><code class="prism language-&quot;NotActions&quot;:  language-undefined"># kubectl get svc -n ghost
NAME    TYPE           CLUSTER-IP      EXTERNAL-IP       PORT(S)        AGE
ghost   LoadBalancer   10.255.100.23   115.144.174.247   80:31435/TCP   4h36</code></pre> 
<blockquote>
<p><strong>해당 Service는 향후 Task 2 POD 배포시 사용될 예정</strong></p>
</blockquote>
<p><a href="https://github.com/netappkr/NDX_Handsonworkshop-/">메인 메뉴로 이동</a></p>

