## PaaS-Ta 설치 및 기술지원 이력 (2019.09)

## 이전 이력 
* [2019.08](./instll-history.md)

### 환경
* OS / HW : **CentOS 6.10** ,  Mem 128GB / HDD 600GB
* bosh : bosh-lite, Git-Hub에서 직접 받은 설치 파일 
* Paas-Ta : 버전 4.0 , Paas-Ta 교육 가이드 문서링크 설치파일 (http://amedio0222.ipdisk.co.kr/pubdav/HDD2/dev/paasta-4.0.tar) 
* VirtualBox :  5.2.20 및 5.2.32r132073
* PaaS-Ta 질의응답 내용  : https://paas-ta.kr/notice/qna_view/400


### 설치 결과 
 
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
**api서버 (10.244.0.134)의 'routing-api'의 출력로그 **

<pre><code> 
goroutine 1 [running]:
code.cloudfoundry.org/lager.(*logger).Fatal(0xc00023db00, 0xafed3a, 0x1e, 0xb9e980, 0x10bf060, 0x0, 0x0, 0x0)
        /var/vcap/data/compile/routing-api/src/code.cloudfoundry.org/lager/logger.go:154 +0x4fc
main.main()
        /var/vcap/data/compile/routing-api/src/code.cloudfoundry.org/routing-api/cmd/routing-api/main.go:145 +0x18e0
2019/08/14 15:08:26 grpc: addrConn.resetTransport failed to create client transport: connection error: desc = "transport: dial tcp: operation was canceled"; Reconnecting to {locket.service.cf.internal:8891 }
panic: context deadline exceeded
</code></pre>

