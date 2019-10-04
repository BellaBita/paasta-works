## PaaS-Ta 설치 및 기술지원 이력 (2019.09)

## 이전 이력 
* [2019.08](./instll-history.md)

### 환경
* OS / HW : Ubuntu 18.04.3 LTS ,  Mem 128GB / HDD 600GB
* bosh : bosh-lite, Git-Hub에서 직접 받은 설치 파일 
* Paas-Ta : 버전 4.0 , Paas-Ta 교육 가이드 문서링크 설치파일 (http://amedio0222.ipdisk.co.kr/pubdav/HDD2/dev/paasta-4.0.tar) 
* VirtualBox :  5.2.20 및 5.2.32r132073
* PaaS-Ta 질의응답 #448 : https://paas-ta.kr/notice/qna_view/488

### 9/19 가이드 메일에 대한  (from paasta@paas-ta.kr)
<pre>
안녕하세요!

문의하신 bosh-lite 설치관련하여 답변드립니다.

paasta의 api vm에 routing-api가 업데이트는 되었지만 정상 동작을 하지않아 routing vm배포가 제대로 되지 않는것으로 확인이 됩니다.

우선 아래와 같이 확인 부탁드립니다.
1. bosh -e vbox vms명령어를 통하여 api vms 접속
   - bosh -e vbox -d paasta ssh api
2. sudo 권한으로 변경
3. monit stop routing 후 monit start routing
4. bosh-lite 재배포 테스트

위의 작업을 진행하였지만 배포가 안되시면 아래와 같은 내용을 첨부하여 재질문 해 주시기 바랍니다.

1. 물리장비  os 및 버전
2. 설치한 virtualbox 버전
3. bosh-lite 배포시 사용한 bosh-deployment안의 bosh.yml 
4. bosh-lite 배포시 사용한 paasta-deployment.yml
5. virtual box Local Route 설정

감사합니다.
</pre>

### 결과 
 
##### <<./deploy-bosh-lite.sh의 출력로그>>
<pre><code>
Task 32 | 17:29:20 | Preparing deployment: Preparing deployment (00:00:35)
Task 32 | 17:29:55 | Preparing deployment: Rendering templates (00:00:20)
Task 32 | 17:30:16 | Preparing package compilation: Finding packages to compile (00:00:01)
Task 32 | 17:30:17 | Compiling packages: pid_utils/37ad75a08069799778151b31e124e28112be659f
Task 32 | 17:30:17 | Compiling packages: proxy/74970cceed3c4c838ebc13eaee8aafd7593839f9
Task 32 | 17:30:17 | Compiling packages: postgres-9.6.10/df1b13a169335f89748b980497594c473ce7148d
...
Task 32 | 17:45:45 | Updating instance api: api/625232be-e425-4577-98e1-90712727aec3 (0) (canary) (00:02:24)
Task 32 | 17:45:45 | Updating instance router: router/5ed00929-aea4-4c6e-bf4f-2aa3c19e7e2c (0) (canary) (00:20:15)
                   L Error: 'router/5ed00929-aea4-4c6e-bf4f-2aa3c19e7e2c (0)' is not running after update. Review logs for failed jobs: gorouter
Task 32 | 18:06:00 | Error: 'router/5ed00929-aea4-4c6e-bf4f-2aa3c19e7e2c (0)' is not running after update. Review logs for failed jobs: gorouter

Task 32 Started  Tue Aug 13 17:29:20 UTC 2019
Task 32 Finished Tue Aug 13 18:06:00 UTC 2019
Task 32 Duration 00:36:40
Task 32 error
</code></pre>

#### <<api서버 문제발생 내역 >>
**api서버 (10.244.0.134)의 'routing-api'가 제데로 기동하지 못하는 데서 기인하는 것 같고, 오류는 아래와 같이 출력**

<pre><code> 
 
goroutine 1 [running]:
code.cloudfoundry.org/lager.(*logger).Fatal(0xc00023db00, 0xafed3a, 0x1e, 0xb9e980, 0x10bf060, 0x0, 0x0, 0x0)
        /var/vcap/data/compile/routing-api/src/code.cloudfoundry.org/lager/logger.go:154 +0x4fc
main.main()
        /var/vcap/data/compile/routing-api/src/code.cloudfoundry.org/routing-api/cmd/routing-api/main.go:145 +0x18e0
2019/08/14 15:08:26 grpc: addrConn.resetTransport failed to create client transport: connection error: desc = "transport: dial tcp: operation was canceled"; Reconnecting to {locket.service.cf.internal:8891 }
panic: context deadline exceeded

</code></pre>
