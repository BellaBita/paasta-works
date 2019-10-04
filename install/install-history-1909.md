## PaaS-Ta 설치 및 기술지원 이력 (2019.09)

## 이전 이력 
* [2019.08](./instll-history-1908.md)

### 환경
* OS / HW : Ubuntu 18.04.3 LTS ,  Mem 128GB / HDD 600GB
* bosh : bosh-lite, Git-Hub에서 직접 받은 설치 파일 
* Paas-Ta : 버전 4.0 , Paas-Ta 교육 가이드 문서링크 설치파일 (http://amedio0222.ipdisk.co.kr/pubdav/HDD2/dev/paasta-4.0.tar) 
* VirtualBox :  5.2.20 및 5.2.32r132073
* PaaS-Ta 질의응답 #448 : https://paas-ta.kr/notice/qna_view/488

### 9/19 가이드 메일  (from paasta@paas-ta.kr)
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

### 위의 가이드 메일데로 조치한 내역 
<pre>
1. monit stop routing, monit start routing 실행 결과 
    bosh로 api VM에 접속후 다음 명령을 실행 
api/3c985c5e-61ee-4575-a51a-12fa1ede6d1c:/var/vcap$ sudo /var/vcap/bosh/bin/monit stop routing
**monit: action failed −− There is no service by that name**
api/3c985c5e-61ee-4575-a51a-12fa1ede6d1c:/var/vcap$ sudo /var/vcap/bosh/bin/monit start routing
**monit: action failed −− There is no service by that name**

2. bosh-lite 재배포
./deploy-bosh-lite.sh 실행결과 

Task 618

Task 618 | 00:57:14 | Preparing deployment: Preparing deployment (00:00:38)
Task 618 | 00:57:52 | Preparing deployment: Rendering templates (00:00:23)
Task 618 | 00:58:16 | Preparing package compilation: Finding packages to compile (00:00:01)
Task 618 | 00:58:18 | Updating instance api: api/3c985c5e-61ee-4575-a51a-12fa1ede6d1c (0) (canary)       (00:01:57)
Task 618 | 01:00:15 | Updating instance router: router/c2428899-c8bd-4bac-9ca9-ac4ee2041746 (0) (canary) (00:20:31)
                    L Error: 'router/c2428899-c8bd-4bac-9ca9-ac4ee2041746 (0)' is not running after update. Review logs for failed jobs: gorouter
Task 618 | 01:20:47 | Error: 'router/c2428899-c8bd-4bac-9ca9-ac4ee2041746 (0)' is not running after update. Review logs for failed jobs: gorouter

Task 618 Started  Thu Sep 26 00:57:14 UTC 2019
Task 618 Finished Thu Sep 26 01:20:47 UTC 2019
Task 618 Duration 00:23:33
Task 618 error
</pre>

> ##### 문의사항에 대한 답변
> 1. 물리장비  os 및 버전
>>  * Ubuntu 18.04.3 LTS

> 2. 설치한 virtualbox 버전</b>
>> * 5.2.20r125813
> 3. bosh-lite 배포시 사용한 bosh-deployment안의 bosh.yml  
>>  * 링크 참조 : [bosh.yml](./201909/bosh.yml) 
> 4. bosh-lite 배포시 사용한 paasta-deployment.yml : 첨부참조
>>  * 링크 참조 : [paasta-deployment.yml](./201909/paasta-deployment.yml)
> 5. virtual box Local Route 설정
>> ubuntu@lena-srv:~$ ip route
>> 10.244.0.0/16 via 192.168.50.6 dev vboxnet0
>> 192.168.50.0/24 dev vboxnet0 proto kernel scope link src 192.168.50.1


### 설치 결과 
 
##### <<./deploy-bosh-lite.sh의 출력로그>>
<pre><code>
Task 618

Task 618 | 00:57:14 | Preparing deployment: Preparing deployment (00:00:38)
Task 618 | 00:57:52 | Preparing deployment: Rendering templates (00:00:23)
Task 618 | 00:58:16 | Preparing package compilation: Finding packages to compile (00:00:01)
Task 618 | 00:58:18 | Updating instance api: api/3c985c5e-61ee-4575-a51a-12fa1ede6d1c (0) (canary)       (00:01:57)
Task 618 | 01:00:15 | Updating instance router: router/c2428899-c8bd-4bac-9ca9-ac4ee2041746 (0) (canary) (00:20:31)
                    L Error: 'router/c2428899-c8bd-4bac-9ca9-ac4ee2041746 (0)' is not running after update. Review logs for failed jobs: gorouter
Task 618 | 01:20:47 | Error: 'router/c2428899-c8bd-4bac-9ca9-ac4ee2041746 (0)' is not running after update. Review logs for failed jobs: gorouter
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

