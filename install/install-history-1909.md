## PaaS-Ta 설치 및 기술지원 이력 (2019.09)

## 이전 이력 
* [2019.08](./instll-history.md)

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

<pre>
1. 물리장비  os 및 버전
ubuntu@lena-srv:~$ cat /etc/issue
Ubuntu 18.04.3 LTS

2. 설치한 virtualbox 버전
ubuntu@lena-srv:~$ vboxmanage −−version
5.2.20r125813

3. bosh-lite 배포시 사용한 bosh-deployment안의 bosh.yml  : 첨부참조 (bosh.yml, paasta-deployment.yml 합본)
4. bosh-lite 배포시 사용한 paasta-deployment.yml : 첨부참조

5. virtual box Local Route 설정
ubuntu@lena-srv:~$ ip route
10.244.0.0/16 via 192.168.50.6 dev vboxnet0
192.168.50.0/24 dev vboxnet0 proto kernel scope link src 192.168.50.1
</pre>

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


## 기타 : 메일 내용으로 요청한 파일

### bosh.yml 
<pre><code>
cloud_provider:
  cert: ((mbus_bootstrap_ssl))
  mbus: https://mbus:((mbus_bootstrap_password))@((internal_ip)):6868
  properties:
    agent:
      mbus: https://mbus:((mbus_bootstrap_password))@0.0.0.0:6868
    blobstore:
      path: /var/vcap/micro_bosh/data/cache
      provider: local
    ntp:
    - time1.google.com
    - time2.google.com
    - time3.google.com
    - time4.google.com
disk_pools:
- disk_size: 65536
  name: disks
instance_groups:
- instances: 1
  jobs:
  - name: bpm
    release: bpm
  - name: nats
    release: bosh
  - name: postgres-10
    release: bosh
  - name: blobstore
    release: bosh
  - name: director
    release: bosh
  - name: health_monitor
    release: bosh
  name: bosh
  networks:
  - name: default
    static_ips:
    - ((internal_ip))
  persistent_disk_pool: disks
  properties:
    agent:
      env:
        bosh:
          blobstores:
          - options:
              endpoint: https://((internal_ip)):25250
              password: ((blobstore_agent_password))
              tls:
                cert:
                  ca: ((blobstore_ca.certificate))
              user: agent
            provider: dav
      mbus: nats://nats:((nats_password))@((internal_ip)):4222
    blobstore:
      address: ((internal_ip))
      agent:
        password: ((blobstore_agent_password))
        user: agent
      director:
        password: ((blobstore_director_password))
        user: director
      port: 25250
      provider: dav
      secret: ((blobstore_secret))
      tls:
        cert:
          ca: ((blobstore_ca.certificate))
          certificate: ((blobstore_server_tls.certificate))
          private_key: ((blobstore_server_tls.private_key))
    director:
      address: 127.0.0.1
      db:
        adapter: postgres
        database: bosh
        host: 127.0.0.1
        listen_address: 127.0.0.1
        password: ((postgres_password))
        user: postgres
      enable_dedicated_status_worker: true
      enable_nats_delivered_templates: true
      enable_post_deploy: true
      events:
        record_events: true
      flush_arp: true
      generate_vm_passwords: true
      local_dns:
        enabled: true
      name: ((director_name))
      ssl:
        cert: ((director_ssl.certificate))
        key: ((director_ssl.private_key))
      user_management:
        local:
          users:
          - name: admin
            password: ((admin_password))
          - name: hm
            password: ((hm_password))
        provider: local
      workers: 4
    hm:
      director_account:
        ca_cert: ((director_ssl.ca))
        password: ((hm_password))
        user: hm
      resurrector_enabled: true
    nats:
      address: ((internal_ip))
      password: ((nats_password))
      tls:
        ca: ((nats_server_tls.ca))
        client_ca:
          certificate: ((nats_ca.certificate))
          private_key: ((nats_ca.private_key))
        director:
          certificate: ((nats_clients_director_tls.certificate))
          private_key: ((nats_clients_director_tls.private_key))
        health_monitor:
          certificate: ((nats_clients_health_monitor_tls.certificate))
          private_key: ((nats_clients_health_monitor_tls.private_key))
        server:
          certificate: ((nats_server_tls.certificate))
          private_key: ((nats_server_tls.private_key))
      user: nats
    ntp:
    - time1.google.com
    - time2.google.com
    - time3.google.com
    - time4.google.com
    postgres:
      adapter: postgres
      database: bosh
      host: 127.0.0.1
      listen_address: 127.0.0.1
      password: ((postgres_password))
      user: postgres
  resource_pool: vms
name: bosh
networks:
- name: default
  subnets:
  - dns:
    - 8.8.8.8
    gateway: ((internal_gw))
    range: ((internal_cidr))
    static:
    - ((internal_ip))
  type: manual
releases:
- name: bosh
  sha1: 60e312a975525aac8905f69dbd9bf43413aadcfd
  url: https://s3.amazonaws.com/bosh-compiled-release-tarballs/bosh-270.5.0-ubuntu-xenial-456.16-20190831-005723-584246861-20190831005731.tgz
  version: 270.5.0
- name: bpm
  sha1: c5d65519a525d43c581135eb5739dc6a31fed8d6
  url: https://s3.amazonaws.com/bosh-compiled-release-tarballs/bpm-1.1.3-ubuntu-xenial-456.16-20190831-004207-318450132-20190831004214.tgz
  version: 1.1.3
resource_pools:
- env:
    bosh:
      mbus:
        cert: ((mbus_bootstrap_ssl))
      password: '*'
  name: vms
  network: default
variables:
- name: admin_password
  type: password
- name: blobstore_director_password
  type: password
- name: blobstore_agent_password
  type: password
- name: hm_password
  type: password
- name: mbus_bootstrap_password
  type: password
- name: nats_password
  type: password
- name: postgres_password
  type: password
- name: blobstore_secret
  type: password
- name: default_ca
  options:
    common_name: ca
    is_ca: true
  type: certificate
- name: mbus_bootstrap_ssl
  options:
    alternative_names:
    - ((internal_ip))
    ca: default_ca
    common_name: ((internal_ip))
  type: certificate
- name: director_ssl
  options:
    alternative_names:
    - ((internal_ip))
    ca: default_ca
    common_name: ((internal_ip))
  type: certificate
- name: nats_ca
  options:
    common_name: default.nats-ca.bosh-internal
    is_ca: true
  type: certificate
- name: nats_server_tls
  options:
    alternative_names:
    - ((internal_ip))
    ca: nats_ca
    common_name: default.nats.bosh-internal
    extended_key_usage:
    - server_auth
  type: certificate
- name: nats_clients_director_tls
  options:
    ca: nats_ca
    common_name: default.director.bosh-internal
    extended_key_usage:
    - client_auth
  type: certificate
- name: nats_clients_health_monitor_tls
  options:
    ca: nats_ca
    common_name: default.hm.bosh-internal
    extended_key_usage:
    - client_auth
  type: certificate
- name: blobstore_ca
  options:
    common_name: default.blobstore-ca.bosh-internal
    is_ca: true
  type: certificate
- name: blobstore_server_tls
  options:
    alternative_names:
    - ((internal_ip))
    ca: blobstore_ca
    common_name: ((internal_ip))
  type: certificate
</code></pre>

### paasta-deployment.yml
---
name: paasta
manifest_version: v4.0
update:
  canaries: 1
  canary_watch_time: 30000-1200000
  max_in_flight: 1
  serial: false
  update_watch_time: 5000-1200000
addons:
- name: loggregator_agent
  include:
    stemcell:
    - os: ubuntu-xenial
  exclude:
    jobs:
    - name: smoke_tests
      release: cf-smoke-tests
  jobs:
  - name: loggregator_agent
    release: loggregator-agent
    properties:
      loggregator:
        tls:
          ca_cert: "((loggregator_ca.certificate))"
          agent:
            cert: "((loggregator_tls_agent.certificate))"
            key: "((loggregator_tls_agent.private_key))"
- name: bpm
  include:
    stemcell:
    - os: ubuntu-xenial
  jobs:
  - name: bpm
    release: bpm
- name: bosh-dns-aliases
  jobs:
  - name: bosh-dns-aliases
    release: bosh-dns-aliases
    properties:
      aliases:
      - domain: '_.cell.service.cf.internal'
        targets:
        - query: '_'
          instance_group: diego-cell
          deployment: paasta
          network: default
          domain: bosh
        - query: '_'
          instance_group: windows-cell
          deployment: paasta 
          network: default
          domain: bosh
        - query: '_'
          instance_group: windows2012R2-cell
          deployment: paasta
          network: default
          domain: bosh
        - query: '_'
          instance_group: windows2016-cell
          deployment: paasta
          network: default
          domain: bosh
        - query: '_'
          instance_group: isolated-diego-cell
          deployment: paasta
          network: default
          domain: bosh
      - domain: auctioneer.service.cf.internal
        targets:
        - query: 'q-s4'
          instance_group: scheduler
          deployment: paasta
          network: default
          domain: bosh
      - domain: bbs.service.cf.internal
        targets:
        - query: 'q-s4'
          instance_group: diego-api
          deployment: paasta
          network: default
          domain: bosh
      - domain: bits.service.cf.internal
        targets:
        - query: '*'
          instance_group: bits
          deployment: paasta
          network: default
          domain: bosh
      - domain: blobstore.service.cf.internal
        targets:
        - query: '*'
          instance_group: singleton-blobstore
          deployment: paasta
          network: default
          domain: bosh
      - domain: cc-uploader.service.cf.internal
        targets:
        - query: '*'
          instance_group: api
          deployment: paasta
          network: default
          domain: bosh
      - domain: cloud-controller-ng.service.cf.internal
        targets:
        - query: '*'
          instance_group: api
          deployment: paasta
          network: default
          domain: bosh
      - domain: credhub.service.cf.internal
        targets:
        - query: '*'
          instance_group: credhub
          deployment: paasta
          network: default
          domain: bosh
      - domain: doppler.service.cf.internal
        targets:
        - query: '*'
          instance_group: doppler
          deployment: paasta
          network: default
          domain: bosh
      - domain: file-server.service.cf.internal
        targets:
        - query: '*'
          instance_group: api
          deployment: paasta
          network: default
          domain: bosh
      - domain: gorouter.service.cf.internal
        targets:
        - query: '*'
          instance_group: router
          deployment: paasta
          network: default
          domain: bosh
      - domain: locket.service.cf.internal
        targets:
        - query: '*'
          instance_group: diego-api
          deployment: paasta
          network: default
          domain: bosh
      - domain: loggregator-trafficcontroller.service.cf.internal
        targets:
        - query: '*'
          instance_group: log-api
          deployment: paasta
          network: default
          domain: bosh
      - domain: policy-server.service.cf.internal
        targets:
        - query: '*'
          instance_group: api
          deployment: paasta
          network: default
          domain: bosh
      - domain: reverse-log-proxy.service.cf.internal
        targets:
        - query: '*'
          instance_group: log-api
          deployment: paasta
          network: default
          domain: bosh
      - domain: routing-api.service.cf.internal
        targets:
        - query: '*'
          instance_group: api
          deployment: paasta
          network: default
          domain: bosh
      - domain: silk-controller.service.cf.internal
        targets:
        - query: '*'
          instance_group: diego-api
          deployment: paasta
          network: default
          domain: bosh
      - domain: sql-db.service.cf.internal
        targets:
        - query: '*'
          instance_group: database
          deployment: paasta
          network: default
          domain: bosh
      - domain: ssh-proxy.service.cf.internal
        targets:
        - query: '*'
          instance_group: scheduler
          deployment: paasta
          network: default
          domain: bosh
      - domain: tps.service.cf.internal
        targets:
        - query: '*'
          instance_group: scheduler
          deployment: paasta
          network: default
          domain: bosh
      - domain: uaa.service.cf.internal
        targets:
        - query: '*'
          instance_group: uaa
          deployment: paasta
          network: default
          domain: bosh

instance_groups:
- name: smoke-tests
  lifecycle: errand
  azs:
  - z1
  instances: 1
  vm_type: minimal
  stemcell: default
  update:
    serial: true
  networks:
  - name: default
  jobs:
  - name: smoke_tests
    release: cf-smoke-tests
    properties:
      smoke_tests:
        api: "https://api.((system_domain))"
        apps_domain: "((system_domain))"
        user: bosh
        password: "((cf_bosh_password))"
        org: cf_smoke_tests_org
        space: cf_smoke_tests_space
        cf_dial_timeout_in_seconds: 300
        skip_ssl_validation: true
  - name: cf-cli-6-linux
    release: cf-cli
- name: nats
  azs:
  - z1
  - z2
  instances: 2
  vm_type: minimal
  stemcell: default
  networks:
  - name: default
  jobs:
  - name: nats
    release: nats
    provides:
      nats: {as: nats, shared: true}
    properties:
      nats:
        password: "((nats_password))"
        user: nats
- name: adapter
  azs:
  - z2
  - z3
  instances: 2
  vm_type: minimal
  stemcell: default
  networks:
  - name: default
  jobs:
  - name: adapter
    release: cf-syslog-drain
    properties:
      scalablesyslog:
        adapter:
          tls:
            ca: "((loggregator_ca.certificate))"
            cert: "((adapter_tls.certificate))"
            key: "((adapter_tls.private_key))"
            cn: ss-adapter
          logs:
            addr: reverse-log-proxy.service.cf.internal:8082
        adapter_rlp:
          tls:
            ca: "((loggregator_ca.certificate))"
            cert: "((adapter_rlp_tls.certificate))"
            key: "((adapter_rlp_tls.private_key))"
            cn: reverselogproxy
- name: database
  migrated_from:
  - name: mysql
  - name: singleton-database
  azs:
  - z3
  persistent_disk_type: 10GB
  instances: 1
  vm_type: default 
  stemcell: default
  update:
    serial: true
  networks:
  - name: default
  jobs:
  - name: mysql
    release: cf-mysql
    properties:
      cf_mysql:
        mysql:
          admin_password: "((cf_mysql_mysql_admin_password))"
          port: 13306
          binlog_enabled: false
          cluster_health:
            password: "((cf_mysql_mysql_cluster_health_password))"
          galera_healthcheck:
            db_password: "((cf_mysql_mysql_galera_healthcheck_password))"
            endpoint_username: galera_healthcheck
            endpoint_password: "((cf_mysql_mysql_galera_healthcheck_endpoint_password))"
          seeded_databases:
          - name: cloud_controller
            username: cloud_controller
            password: "((cc_database_password))"
          - name: credhub
            password: ((credhub_database_password))
            username: credhub
          - name: diego
            username: diego
            password: "((diego_database_password))"
          - name: network_connectivity
            username: network_connectivity
            password: "((network_connectivity_database_password))"
          - name: network_policy
            username: network_policy
            password: "((network_policy_database_password))"
          - name: routing-api
            username: routing-api
            password: "((routing_api_database_password))"
          - name: uaa
            username: uaa
            password: "((uaa_database_password))"
          - name: locket
            username: locket
            password: "((locket_database_password))"
  - name: proxy
    release: cf-mysql
    properties:
      cf_mysql:
        proxy:
          api_password: "((cf_mysql_proxy_api_password))"
- name: diego-api
  migrated_from:
  - name: diego-bbs
  azs:
  - z3
  - z2
  instances: 2
  vm_type: small
  stemcell: default
  update:
    serial: true
  networks:
  - name: default
  jobs:
  - name: cfdot
    release: diego
    properties:
      tls: &cfdot_tls_client_properties
        ca_certificate: "((service_cf_internal_ca.certificate))"
        certificate: "((diego_rep_client.certificate))"
        private_key: "((diego_rep_client.private_key))"
  - name: bbs
    release: diego
    properties:
      bpm:
        enabled: true
      diego:
        bbs:
          active_key_label: key-2016-06
          detect_consul_cell_registrations: false
          encryption_keys:
          - label: key-2016-06
            passphrase: "((diego_bbs_encryption_keys_passphrase))"
          sql:
            db_host: sql-db.service.cf.internal
            db_port: 3306
            db_schema: diego
            db_username: diego
            db_password: "((diego_database_password))"
            db_driver: mysql
          ca_cert: "((service_cf_internal_ca.certificate))"
          auctioneer: &diego_auctioneer_client_properties
            ca_cert: "((service_cf_internal_ca.certificate))"
            client_cert: "((diego_auctioneer_client.certificate))"
            client_key: "((diego_auctioneer_client.private_key))"
          server_cert: "((diego_bbs_server.certificate))"
          server_key: "((diego_bbs_server.private_key))"
          skip_consul_lock: true
          rep:
            require_tls: true
            ca_cert: "((service_cf_internal_ca.certificate))"
            client_cert: "((diego_rep_client.certificate))"
            client_key: "((diego_rep_client.private_key))"
      enable_consul_service_registration: false
      loggregator: &diego_loggregator_client_properties
        use_v2_api: true
        ca_cert: "((loggregator_ca.certificate))"
        cert: "((loggregator_tls_agent.certificate))"
        key: "((loggregator_tls_agent.private_key))"
      logging:
        format:
          timestamp: "rfc3339"
  - name: silk-controller
    release: silk
    properties:
      ca_cert: ((silk_ca.certificate))
      server_cert: ((silk_controller.certificate))
      server_key: ((silk_controller.private_key))
      database:
        type: mysql
        username: network_connectivity
        password: ((network_connectivity_database_password))
        host: sql-db.service.cf.internal
        port: 3306
        name: network_connectivity
      silk_daemon:
        ca_cert: ((silk_ca.certificate))
        client_cert: ((silk_daemon.certificate))
        client_key: ((silk_daemon.private_key))
  - name: locket
    release: diego
    properties:
      bpm:
        enabled: true
      tls:
        ca_cert: "((service_cf_internal_ca.certificate))"
        cert: "((diego_locket_server.certificate))"
        key: "((diego_locket_server.private_key))"
      diego:
        locket:
          sql:
            db_host: sql-db.service.cf.internal
            db_port: 3306
            db_schema: locket
            db_username: locket
            db_password: "((locket_database_password))"
            db_driver: mysql
      enable_consul_service_registration: false
      loggregator:
        use_v2_api: true
        ca_cert: "((loggregator_ca.certificate))"
        cert: "((loggregator_tls_agent.certificate))"
        key: "((loggregator_tls_agent.private_key))"
      logging:
        format:
          timestamp: "rfc3339"
- name: uaa
  azs:
  - z2
  - z3
  instances: 2
  vm_type: default
  stemcell: default
  networks:
  - name: default
  jobs:
  - name: uaa
    release: uaa
    properties:
      bpm:
        enabled: true
      encryption:
        active_key_label: default_key
        encryption_keys:
          - label: default_key
            passphrase: ((uaa_default_encryption_passphrase))
      login:
        saml:
          activeKeyId: key-1
          keys:
            key-1:
              key: "((uaa_login_saml.private_key))"
              certificate: "((uaa_login_saml.certificate))"
              passphrase: ""
##### PaaS-TA Portal Properties Add Start
#        logout:
#          redirect:
#            parameter:
#              disable: "((uaa_login_logout_redirect_parameter_disable))"
#              whitelist: "((uaa_login_logout_redirect_parameter_whitelist))"
#        branding:
#          company_name: "((uaa_login_branding_company_name))"
#          product_logo: "((uaa_login_branding_product_logo))"
#          square_logo: "((uaa_login_branding_square_logo))"
#          footer_legal_text: "((uaa_login_branding_footer_legal_text))"
#        links:
#          passwd: "((uaa_login_links_passwd))"
#          signup: "((uaa_login_links_signup))"
##### PaaS-TA Portal Properties Add End
      uaa:
        sslCertificate: "((uaa_ssl.certificate))"
        sslPrivateKey: "((uaa_ssl.private_key))"
        zones:
          internal:
            hostnames:
            - uaa.service.cf.internal
        url: https://uaa.((system_domain))
        admin:
          client_secret: "((uaa_admin_client_secret))"
        logging_level: INFO
        scim:
          users:
          - name: admin
            password: "((cf_admin_password))"
            groups:
            - cloud_controller.admin
            - doppler.firehose
            - network.admin
            - openid
            - routing.router_groups.read
            - routing.router_groups.write
            - scim.read
            - scim.write
          - name: bosh
            password: "((cf_bosh_password))"
            groups:
            - cloud_controller.admin
            - doppler.firehose
            - openid
            - routing.router_groups.read
            - routing.router_groups.write
            - scim.read
            - scim.write
        jwt:
          policy:
            active_key_id: key-1
            keys:
              key-1:
                signingKey: "((uaa_jwt_signing_key.private_key))"
        clients:
          monitoring:
            authorities: oauth.login,scim.write,clients.read,scim.userids,password.write,clients.secret,clients.write,uaa.admin,scim.read,doppler.firehose
            override: true
            authorized-grant-types: authorization_code,client_credentials,password,implicit,refresh_token
            scope: cloud_controller.read,cloud_controller.write,openid,cloud_controller.admin,scim.read,scim.write,doppler.firehose,uaa.user,routing.router_groups.read,uaa.admin,password.write
            redirect-uri: "https://uaa.((system_domain))/login"
            secret: monit-secret
          firehose-to-syslog:
            authorities: uaa.none, doppler.firehose
            override: true
            authorized-grant-types: client_credentials
            scope: network.admin,network.write,cloud_controller.read,cloud_controller.write,openid,password.write,cloud_controller.admin,scim.read,scim.write,doppler.firehose,uaa.user,routing.router_groups.read,routing.router_groups.write,cloud_controller.admin_read_only,cloud_controller.global_auditor
            secret: firehose-secret
          cc_routing:
            authorities: routing.router_groups.read
            authorized-grant-types: client_credentials
            secret: "((uaa_clients_cc-routing_secret))"
          cc-service-dashboards:
            authorities: clients.read,clients.write,clients.admin
            authorized-grant-types: client_credentials
            scope: openid,cloud_controller_service_permissions.read
            secret: "((uaa_clients_cc-service-dashboards_secret))"
          cc_service_key_client:
            authorities: credhub.read,credhub.write
            authorized-grant-types: client_credentials
            secret: "((uaa_clients_cc_service_key_client_secret))"
          cf:
            access-token-validity: 600
            authorities: uaa.none
            authorized-grant-types: password,refresh_token
            override: true
            refresh-token-validity: 2592000
            scope: network.admin,network.write,cloud_controller.read,cloud_controller.write,openid,password.write,cloud_controller.admin,scim.read,scim.write,doppler.firehose,uaa.user,routing.router_groups.read,routing.router_groups.write,cloud_controller.admin_read_only,cloud_controller.global_auditor,perm.admin
            secret: ''
          cloud_controller_username_lookup:
            authorities: scim.userids
            authorized-grant-types: client_credentials
            secret: "((uaa_clients_cloud_controller_username_lookup_secret))"
          credhub_admin_client:
            authorities: credhub.read,credhub.write
            authorized-grant-types: client_credentials
            secret: ((credhub_admin_client_secret))
          doppler:
            authorities: uaa.resource
            override: true
            authorized-grant-types: client_credentials
            secret: "((uaa_clients_doppler_secret))"
          gorouter:
            authorities: routing.routes.read
            authorized-grant-types: client_credentials
            secret: "((uaa_clients_gorouter_secret))"
          ssh-proxy:
            authorized-grant-types: authorization_code
            autoapprove: true
            override: true
            redirect-uri: "https://uaa.((system_domain))/login"
            scope: openid,cloud_controller.read,cloud_controller.write,cloud_controller.admin
            secret: "((uaa_clients_ssh-proxy_secret))"
          routing_api_client:
            authorities: routing.routes.write,routing.routes.read,routing.router_groups.read
            authorized-grant-types: client_credentials
            secret: "((uaa_clients_routing_api_client_secret))"
          network-policy:
            authorities: uaa.resource,cloud_controller.admin_read_only
            authorized-grant-types: client_credentials
            secret: ((uaa_clients_network_policy_secret))
          tcp_emitter:
            authorities: routing.routes.write,routing.routes.read
            authorized-grant-types: client_credentials
            secret: "((uaa_clients_tcp_emitter_secret))"
          tcp_router:
            authorities: routing.routes.read
            authorized-grant-types: client_credentials
            secret: "((uaa_clients_tcp_router_secret))"
##### PaaS-TA Portal User Account Add Start
#          portalclient:
#            redirect-uri: "((uaa_clients_portalclient_redirect_uri))"
#            scope: cloud_controller_service_permissions.read,openid,cloud_controller.read,cloud_controller.write,cloud_controller.admin
#            autoapprove: openid,cloud_controller_service_permissions.read
#            authorities: uaa.resource
#            authorized-grant-types: authorization_code,client_credentials,refresh_token
#            secret: "((uaa_clients_portalclinet_secret))"
##### PaaS-TA Portal User Account Add End
      uaadb:
        databases:
        - name: uaa
          tag: uaa
        db_scheme: mysql
        port: 3306
        roles:
        - name: uaa
          password: "((uaa_database_password))"
          tag: admin
  - name: route_registrar
    release: routing
    properties:
      route_registrar:
        routes:
        - health_check:
            name: uaa-healthcheck
            script_path: "/var/vcap/jobs/uaa/bin/health_check"
          name: uaa
          port: 8080
          registration_interval: 10s
          tags:
            component: uaa
          uris:
          - uaa.((system_domain))
          - "*.uaa.((system_domain))"
          - login.((system_domain))
          - "*.login.((system_domain))"
  - name: statsd_injector
    release: statsd-injector
    properties: &statsd_injector_properties
      loggregator:
        tls:
          ca_cert: "((loggregator_ca.certificate))"
          statsd_injector:
            cert: "((loggregator_tls_statsdinjector.certificate))"
            key: "((loggregator_tls_statsdinjector.private_key))"
- name: singleton-blobstore
  migrated_from:
  - name: blobstore
  azs:
  - z3
  instances: 1
  vm_type: small
  persistent_disk_type: 100GB
  stemcell: default
  update:
    serial: true
  networks:
  - name: default
  jobs:
  - name: blobstore
    release: capi
    properties:
      bpm:
        enabled: true
      system_domain: "((system_domain))"
      blobstore:
        admin_users:
        - username: blobstore-user
          password: "((blobstore_admin_users_password))"
        secure_link:
          secret: "((blobstore_secure_link_secret))"
        tls:
          cert: "((blobstore_tls.certificate))"
          private_key: "((blobstore_tls.private_key))"
  - name: route_registrar
    release: routing
    properties:
      route_registrar:
        routes:
        - name: blobstore
          port: 8080
          registration_interval: 20s
          tags:
            component: blobstore
          uris:
          - blobstore.((system_domain))
- name: api
  azs:
  - z3
  - z2
  instances: 2
  vm_type: small 
  vm_extensions:
  - 50GB_ephemeral_disk
  stemcell: default
  networks:
  - name: default
  jobs:
  - name: cloud_controller_ng
    release: capi
    provides:
      cloud_controller: {as: cloud_controller, shared: true}
    properties:
      bpm:
        enabled: true
      router:
        route_services_secret: "((router_route_services_secret))"
      system_domain: "((system_domain))"
      app_domains:
      - "((system_domain))"
      app_ssh:
        host_key_fingerprint: "((diego_ssh_proxy_host_key.public_key_fingerprint))"
      routing_api: &routing_api
        enabled: true
      credhub_api:
        ca_cert: ((credhub_ca.certificate))
      ssl:
        skip_cert_verify: true
      uaa:
        ca_cert: "((uaa_ca.certificate))"
        clients:
          cc_routing:
            secret: "((uaa_clients_cc-routing_secret))"
          cloud_controller_username_lookup:
            secret: "((uaa_clients_cloud_controller_username_lookup_secret))"
          cc-service-dashboards:
            secret: "((uaa_clients_cc-service-dashboards_secret))"
          cc_service_key_client:
            secret: "((uaa_clients_cc_service_key_client_secret))"
        url: https://uaa.((system_domain))
      cc:
        stacks:
        - name: cflinuxfs2
          description: Cloud Foundry Linux-based filesystem
        default_running_security_groups:
        - public_networks
        - dns
        default_staging_security_groups:
        - public_networks
        - dns
        security_group_definitions:
        - name: public_networks
          rules:
          - destination: 0.0.0.0-9.255.255.255
            protocol: all
          - destination: 11.0.0.0-169.253.255.255
            protocol: all
          - destination: 169.255.0.0-172.15.255.255
            protocol: all
          - destination: 172.32.0.0-192.167.255.255
            protocol: all
          - destination: 192.169.0.0-255.255.255.255
            protocol: all
        - name: dns
          rules:
          - destination: 0.0.0.0/0
            ports: '53'
            protocol: tcp
          - destination: 0.0.0.0/0
            ports: '53'
            protocol: udp
        install_buildpacks:
        ## Order is important here
        - name: staticfile_buildpack
          package: staticfile-buildpack-cflinuxfs2
        - name: java_buildpack
          package: java-buildpack-cflinuxfs2
        - name: ruby_buildpack
          package: ruby-buildpack-cflinuxfs2
        - name: dotnet_core_buildpack
          package: dotnet-core-buildpack-cflinuxfs2
        - name: nodejs_buildpack
          package: nodejs-buildpack-cflinuxfs2
        - name: go_buildpack
          package: go-buildpack-cflinuxfs2
        - name: python_buildpack
          package: python-buildpack-cflinuxfs2
        - name: php_buildpack
          package: php-buildpack-cflinuxfs2
        - name: binary_buildpack
          package: binary-buildpack-cflinuxfs2
        db_encryption_key: "((cc_db_encryption_key))"
        database_encryption: &cc-database-encryption
          current_key_label: "encryption_key_0"
          keys:
            encryption_key_0: "((cc_db_encryption_key))"
        bulk_api_password: "((cc_bulk_api_password))"
        internal_api_password: "((cc_internal_api_password))"
        staging_upload_user: staging_user
        staging_upload_password: "((cc_staging_upload_password))"
        buildpacks: &blobstore-properties
          blobstore_type: webdav
          webdav_config:
            ca_cert: "((service_cf_internal_ca.certificate))"
            blobstore_timeout: 5
            password: "((blobstore_admin_users_password))"
            private_endpoint: https://blobstore.service.cf.internal:4443
            public_endpoint: https://blobstore.((system_domain))
            username: blobstore-user
        resource_pool: *blobstore-properties
        packages: *blobstore-properties
        droplets: *blobstore-properties
        mutual_tls: &cc_mutual_tls
          ca_cert: "((service_cf_internal_ca.certificate))"
          public_cert: "((cc_tls.certificate))"
          private_key: "((cc_tls.private_key))"
        public_tls:
          ca_cert: "((service_cf_internal_ca.certificate))"
          certificate: "((cc_public_tls.certificate))"
          private_key: "((cc_public_tls.private_key))"
      ccdb: &ccdb
        databases:
        - name: cloud_controller
          tag: cc
        db_scheme: mysql
        port: 3306
        roles:
        - name: cloud_controller
          password: "((cc_database_password))"
          tag: admin
  - name: binary-buildpack
    release: binary-buildpack
  - name: dotnet-core-buildpack
    release: dotnet-core-buildpack
  - name: go-buildpack
    release: go-buildpack
  - name: java-buildpack
    release: java-buildpack
  - name: nodejs-buildpack
    release: nodejs-buildpack
  - name: php-buildpack
    release: php-buildpack
  - name: python-buildpack
    release: python-buildpack
  - name: ruby-buildpack
    release: ruby-buildpack
  - name: staticfile-buildpack
    release: staticfile-buildpack
  - name: route_registrar
    release: routing
    properties:
      route_registrar:
        routes:
        - name: api
          registration_interval: 10s
          port: 9022
          tls_port: 9024
          server_cert_domain_san: "api.((system_domain))"
          tags:
            component: CloudController
          uris:
          - api.((system_domain))
          health_check:
            name: api-health-check
            script_path: "/var/vcap/jobs/cloud_controller_ng/bin/cloud_controller_ng_health_check"
            timeout: 6s
        - name: policy-server
          port: 4002
          registration_interval: 20s
          uris:
          - api.((system_domain))/networking
  - name: statsd_injector
    release: statsd-injector
    properties: *statsd_injector_properties
  - name: file_server
    release: diego
    properties:
      bpm:
        enabled: true
      enable_consul_service_registration: false
      logging:
        format:
          timestamp: "rfc3339"
      loggregator: *diego_loggregator_client_properties
  - name: routing-api
    release: routing
    properties:
      routing_api:
        enabled: true
        skip_consul_lock: true
        system_domain: "((system_domain))"
        router_groups:
        - name: default-tcp
          type: tcp
          reservable_ports: 1024-1123
        sqldb:
          host: sql-db.service.cf.internal
          type: mysql
          port: 3306
          schema: routing-api
          username: routing-api
          password: "((routing_api_database_password))"
        locket:
          api_location: "locket.service.cf.internal:8891"
          ca_cert: "((service_cf_internal_ca.certificate))"
          client_cert: "((diego_locket_client.certificate))"
          client_key: "((diego_locket_client.private_key))"
      uaa:
        ca_cert: "((uaa_ca.certificate))"
        tls_port: 8443
  - name: policy-server
    release: cf-networking
    properties:
      uaa_client_secret: ((uaa_clients_network_policy_secret))
      uaa_ca: ((uaa_ca.certificate))
      enable_space_developer_self_service: true
      database:
        type: mysql
        username: network_policy
        password: ((network_policy_database_password))
        host: sql-db.service.cf.internal
        port: 3306
        name: network_policy
  - name: policy-server-internal
    release: cf-networking
    properties:
      ca_cert: ((network_policy_ca.certificate))
      server_cert: ((network_policy_server.certificate))
      server_key: ((network_policy_server.private_key))
  - name: cc_uploader
    release: capi
    properties:
      bpm:
        enabled: true
      capi:
        cc_uploader:
          cc:
            ca_cert: "((service_cf_internal_ca.certificate))"
            client_cert: "((cc_bridge_cc_uploader.certificate))"
            client_key: "((cc_bridge_cc_uploader.private_key))"
          mutual_tls:
            ca_cert: "((service_cf_internal_ca.certificate))"
            server_cert: "((cc_bridge_cc_uploader_server.certificate))"
            server_key: "((cc_bridge_cc_uploader_server.private_key))"
- name: cc-worker
  azs:
  - z3
  - z2
  instances: 2
  vm_type: minimal 
  stemcell: default
  networks:
  - name: default
  jobs:
  - name: cloud_controller_worker
    release: capi
    properties:
      bpm:
        enabled: true
      cc:
        db_encryption_key: "((cc_db_encryption_key))"
        database_encryption: *cc-database-encryption
        internal_api_password: "((cc_internal_api_password))"
        staging_upload_user: staging_user
        staging_upload_password: "((cc_staging_upload_password))"
        resource_pool: *blobstore-properties
        packages: *blobstore-properties
        droplets: *blobstore-properties
        buildpacks: *blobstore-properties
        mutual_tls: *cc_mutual_tls
      ccdb: *ccdb
      system_domain: "((system_domain))"
      routing_api: *routing_api
      ssl:
        skip_cert_verify: true
      uaa:
        ca_cert: "((uaa_ca.certificate))"
        clients:
          cc-service-dashboards:
            secret: "((uaa_clients_cc-service-dashboards_secret))"
          cc_routing:
            secret: "((uaa_clients_cc-routing_secret))"
- name: router
  azs:
  - z1
  - z2
  - z3
  instances: 3
  vm_type: minimal
  vm_extensions:
  - cf-router-network-properties
  stemcell: default
  update:
    serial: true
  networks:
  - name: default
  jobs:
  - name: gorouter
    release: routing
    properties:
      router:
        enable_ssl: true
        tls_pem:
        - cert_chain: "((router_ssl.certificate))"
          private_key: "((router_ssl.private_key))"
        ca_certs: |
          ((application_ca.certificate))
          ((service_cf_internal_ca.certificate))
        backends:
          enable_tls: true
          cert_chain: ((gorouter_backend_tls.certificate))
          private_key: ((gorouter_backend_tls.private_key))
        status:
          password: "((router_status_password))"
          user: router-status
        route_services_secret: "((router_route_services_secret))"
        tracing:
          enable_zipkin: true
      routing_api:
        enabled: true
      uaa:
        clients:
          gorouter:
            secret: "((uaa_clients_gorouter_secret))"
        ca_cert: "((uaa_ca.certificate))"
        ssl:
          port: 8443
- name: tcp-router
  azs:
  - z2
  - z3
  instances: 1
  vm_type: minimal
  stemcell: default
  vm_extensions:
  - cf-tcp-router-network-properties
  networks:
  - name: default
  jobs:
  - name: tcp_router
    release: routing
    properties:
      tcp_router:
        oauth_secret: "((uaa_clients_tcp_router_secret))"
        router_group: default-tcp
      uaa:
        ca_cert: "((uaa_ca.certificate))"
        tls_port: 8443
- name: scheduler
  azs:
  - z2
  - z3
  instances: 2
  migrated_from:
  - {name: cc-bridge}
  - {name: cc-clock}
  - {name: diego-brain}
  vm_type: small 
  vm_extensions:
  - diego-ssh-proxy-network-properties
  stemcell: default
  update:
    serial: true
  networks:
  - name: default
  jobs:
  - name: cfdot
    release: diego
    properties:
      tls: *cfdot_tls_client_properties
  - name: auctioneer
    release: diego
    properties:
      bpm:
        enabled: true
      diego:
        auctioneer:
          bbs: &diego_bbs_client_properties
            ca_cert: "((service_cf_internal_ca.certificate))"
            client_cert: "((diego_bbs_client.certificate))"
            client_key: "((diego_bbs_client.private_key))"
          ca_cert: "((service_cf_internal_ca.certificate))"
          rep:
            require_tls: true
            ca_cert: "((service_cf_internal_ca.certificate))"
            client_cert: "((diego_rep_client.certificate))"
            client_key: "((diego_rep_client.private_key))"
          server_cert: "((diego_auctioneer_server.certificate))"
          server_key: "((diego_auctioneer_server.private_key))"
          skip_consul_lock: true
      enable_consul_service_registration: false
      loggregator: *diego_loggregator_client_properties
      logging:
        format:
          timestamp: "rfc3339"
  - name: cloud_controller_clock
    release: capi
    properties:
      bpm:
        enabled: true
      cc:
        db_encryption_key: "((cc_db_encryption_key))"
        database_encryption: *cc-database-encryption
        internal_api_password: "((cc_internal_api_password))"
        staging_upload_user: staging_user
        staging_upload_password: "((cc_staging_upload_password))"
        resource_pool: *blobstore-properties
        packages: *blobstore-properties
        droplets: *blobstore-properties
        buildpacks: *blobstore-properties
        mutual_tls: *cc_mutual_tls
      ccdb: *ccdb
      system_domain: "((system_domain))"
      routing_api: *routing_api
      uaa:
        ca_cert: "((uaa_ca.certificate))"
        clients:
          cc-service-dashboards:
            secret: "((uaa_clients_cc-service-dashboards_secret))"
          cc_routing:
            secret: "((uaa_clients_cc-routing_secret))"
        ssl:
          port: 8443
  - name: statsd_injector
    release: statsd-injector
    properties: *statsd_injector_properties
  - name: tps
    release: capi
    properties:
      bpm:
        enabled: true
      capi:
        tps:
          bbs: *diego_bbs_client_properties
          watcher:
            locket:
              api_location: "locket.service.cf.internal:8891"
            skip_consul_lock: true
          cc:
            ca_cert: "((service_cf_internal_ca.certificate))"
            client_cert: "((cc_bridge_tps.certificate))"
            client_key: "((cc_bridge_tps.private_key))"
  - name: ssh_proxy
    release: diego
    properties:
      bpm:
        enabled: true
      diego:
        ssh_proxy:
          enable_cf_auth: true
          host_key: "((diego_ssh_proxy_host_key.private_key))"
          uaa_secret: "((uaa_clients_ssh-proxy_secret))"
          uaa:
            ca_cert: "((uaa_ca.certificate))"
          bbs: *diego_bbs_client_properties
      enable_consul_service_registration: false
      loggregator: *diego_loggregator_client_properties
      logging:
        format:
          timestamp: "rfc3339"
  - name: scheduler
    release: cf-syslog-drain
    properties:
      scalablesyslog:
        scheduler:
          api:
            url: https://cloud-controller-ng.service.cf.internal:9023
          tls:
            client:
              ca: "((loggregator_ca.certificate))"
              cert: "((scheduler_client_tls.certificate))"
              key: "((scheduler_client_tls.private_key))"
              adapter_cn: "ss-adapter"
            api:
              ca: "((service_cf_internal_ca.certificate))"
              cert: "((scheduler_api_tls.certificate))"
              key: "((scheduler_api_tls.private_key))"
              cn: "cloud-controller-ng.service.cf.internal"
  - name: log-cache-scheduler
    release: log-cache
  - name: log-cache-expvar-forwarder
    release: log-cache
    properties:
      counters: []
      gauges: []
      maps:
      - name: worker-assignments
        source_id: log-cache-scheduler
        addr: http://localhost:6064/debug/vars
        template: "{{.WorkerAssignments | jsonMap}}"
      - name: worker-health
        source_id: log-cache-scheduler
        addr: http://localhost:6064/debug/vars
        template: "{{.WorkerHealth | jsonMap}}"
- name: doppler
  azs:
  - z1
  - z2
  instances: 4
  vm_type: minimal 
  stemcell: default
  networks:
  - name: default
  jobs:
  - name: doppler
    release: loggregator
    provides:
      doppler: {as: doppler, shared: true}
    properties:
      loggregator:
        tls:
          ca_cert: "((loggregator_ca.certificate))"
          doppler:
            cert: "((loggregator_tls_doppler.certificate))"
            key: "((loggregator_tls_doppler.private_key))"
  - name: log-cache
    provides:
      log-cache: {shared: true}
    properties:
      egress_port: 8080
      health_addr: localhost:6060
      tls:
        ca_cert: ((log_cache_ca.certificate))
        cert: ((log_cache.certificate))
        key: ((log_cache.private_key))
    release: log-cache
  - name: log-cache-gateway
    properties:
      gateway_addr: localhost:8081
    release: log-cache
  - consumes:
      reverse_log_proxy: {from: reverse_log_proxy}
    name: log-cache-nozzle
    properties:
      logs_provider:
        tls:
          ca_cert: ((loggregator_ca.certificate))
          cert: ((logs_provider.certificate))
          key: ((logs_provider.private_key))
    release: log-cache
  - name: log-cache-expvar-forwarder
    properties:
      maps: []
      counters:
        # Log Cache
      - name: egress
        source_id: log-cache
        addr: http://localhost:6060/debug/vars
        template: "{{.LogCache.Egress}}"
      - name: ingress
        source_id: log-cache
        addr: http://localhost:6060/debug/vars
        template: "{{.LogCache.Ingress}}"
      - name: expired
        source_id: log-cache
        addr: http://localhost:6060/debug/vars
        template: "{{.LogCache.Expired}}"
      - name: dropped
        source_id: log-cache
        addr: http://localhost:6060/debug/vars
        template: "{{.LogCache.Dropped}}"
      - name: promql-timeout
        source_id: log-cache
        addr: http://localhost:6060/debug/vars
        template: "{{.LogCache.PromQLTimeout}}"

        # Nozzle
      - name: egress
        source_id: log-cache-nozzle
        addr: http://localhost:6061/debug/vars
        template: "{{.Nozzle.Egress}}"
      - name: ingress
        source_id: log-cache-nozzle
        addr: http://localhost:6061/debug/vars
        template: "{{.Nozzle.Ingress}}"
      - name: err
        source_id: log-cache-nozzle
        addr: http://localhost:6061/debug/vars
        template: "{{.Nozzle.Err}}"
      - name: dropped
        source_id: log-cache-nozzle
        addr: http://localhost:6061/debug/vars
        template: "{{.Nozzle.Dropped}}"
      gauges:
        # Log Cache
      - name: cache-period
        source_id: log-cache
        addr: http://localhost:6060/debug/vars
        template: "{{.LogCache.CachePeriod}}"
      - name: store-size
        source_id: log-cache
        addr: http://localhost:6060/debug/vars
        template: "{{.LogCache.StoreSize}}"
      - name: total-system-memory
        source_id: log-cache
        addr: http://localhost:6060/debug/vars
        template: "{{.LogCache.TotalSystemMemory}}"
      - name: available-system-memory
        source_id: log-cache
        addr: http://localhost:6060/debug/vars
        template: "{{.LogCache.AvailableSystemMemory}}"
      - name: promql-instant-query-time
        source_id: log-cache
        addr: http://localhost:6060/debug/vars
        template: "{{.LogCache.PromQLInstantQueryTime}}"
      - name: promql-range-query-time
        source_id: log-cache
        addr: http://localhost:6060/debug/vars
        template: "{{.LogCache.PromQLRangeQueryTime}}"

        # CF Auth Proxy
      - name: last-uaa-latency
        source_id: log-cache-cf-auth-proxy
        addr: http://localhost:6065/debug/vars
        template: "{{.CFAuthProxy.LastUAALatency}}"
      - name: last-capi-v3-apps-latency
        source_id: log-cache-cf-auth-proxy
        addr: http://localhost:6065/debug/vars
        template: "{{.CFAuthProxy.LastCAPIV3AppsLatency}}"
      - name: last-capi-v2-list-service-instances-latency
        source_id: log-cache-cf-auth-proxy
        addr: http://localhost:6065/debug/vars
        template: "{{.CFAuthProxy.LastCAPIV2ListServiceInstancesLatency}}"
      - name: last-capi-v4-log-access-latency
        source_id: log-cache-cf-auth-proxy
        addr: http://localhost:6065/debug/vars
        template: "{{.CFAuthProxy.LastCAPIV4LogAccessLatency}}"
      - name: last-capi-v2-service-instances-latency
        source_id: log-cache-cf-auth-proxy
        addr: http://localhost:6065/debug/vars
        template: "{{.CFAuthProxy.LastCAPIV2ServiceInstancesLatency}}"
    release: log-cache
  - name: route_registrar
    properties:
      route_registrar:
        routes:
        - name: log-cache-reverse-proxy
          port: 8083
          registration_interval: 20s
          uris:
          - log-cache.((system_domain))
          - '*.log-cache.((system_domain))'
    release: routing
  - name: log-cache-cf-auth-proxy
    properties:
      cc:
        ca_cert: ((service_cf_internal_ca.certificate))
        capi_internal_addr: https://cloud-controller-ng.service.cf.internal:9023
        cert: ((log_cache_tls_cc_auth_proxy.certificate))
        common_name: cloud-controller-ng.service.cf.internal
        key: ((log_cache_tls_cc_auth_proxy.private_key))
      proxy_port: 8083
      uaa:
        ca_cert: ((uaa_ca.certificate))
        client_id: doppler
        client_secret: ((uaa_clients_doppler_secret))
        internal_addr: https://uaa.service.cf.internal:8443
    release: log-cache
- name: diego-cell
  azs:
  - z3
  - z2
  - z1
  instances: 2
  vm_type: default
  vm_extensions:
  - 100GB_ephemeral_disk
  stemcell: default
  networks:
  - name: default
  jobs:
  - name: cflinuxfs2-rootfs-setup
    release: cflinuxfs2
    properties:
      cflinuxfs2-rootfs:
        trusted_certs: |
          ((application_ca.certificate))
          ((credhub_ca.certificate))
          ((uaa_ca.certificate))
  - name: garden
    release: garden-runc
    properties:
      garden:
        cleanup_process_dirs_on_wait: true
        debug_listen_address: 127.0.0.1:17019
        default_container_grace_time: 0
        destroy_containers_on_start: true
        deny_networks:
        - 0.0.0.0/0
        persistent_image_list:
        - "/var/vcap/packages/cflinuxfs2/rootfs.tar"
#        network_plugin: /var/vcap/packages/runc-cni/bin/garden-external-networker
#        network_plugin_extra_args:
#        - --configFile=/var/vcap/jobs/garden-cni/config/adapter.json
      grootfs:
        reserved_space_for_other_jobs_in_mb: 15360
      logging:
        format:
          timestamp: "rfc3339"
  - name: rep
    release: diego
    properties:
      bpm:
        enabled: true
      diego:
        executor:
          instance_identity_ca_cert: ((diego_instance_identity_ca.certificate))
          instance_identity_key: ((diego_instance_identity_ca.private_key))
        rep:
          preloaded_rootfses:
          - cflinuxfs2:/var/vcap/packages/cflinuxfs2/rootfs.tar
      containers:
        trusted_ca_certificates:
          - ((application_ca.certificate))
          - ((credhub_ca.certificate))
          - ((uaa_ca.certificate))
      enable_consul_service_registration: false
      enable_declarative_healthcheck: true
      loggregator: *diego_loggregator_client_properties
      tls:
        ca_cert: "((service_cf_internal_ca.certificate))"
        cert: "((diego_rep_agent_v2.certificate))"
        key: "((diego_rep_agent_v2.private_key))"
      logging:
        format:
          timestamp: "rfc3339"
  - name: cfdot
    release: diego
    properties:
      tls: *cfdot_tls_client_properties
  - name: route_emitter
    release: diego
    properties:
      bpm:
        enabled: true
      loggregator: *diego_loggregator_client_properties
      diego:
        route_emitter:
          local_mode: true
          bbs:
            ca_cert: "((service_cf_internal_ca.certificate))"
            client_cert: "((diego_bbs_client.certificate))"
            client_key: "((diego_bbs_client.private_key))"
      tcp:
        enabled: true
      uaa:
        ca_cert: "((uaa_ca.certificate))"
        client_secret: "((uaa_clients_tcp_emitter_secret))"
      logging:
        format:
          timestamp: "rfc3339"
#  - name: garden-cni
#    release: cf-networking
#    properties:
#      cni_plugin_dir: /var/vcap/packages/silk-cni/bin
#      cni_config_dir: /var/vcap/jobs/silk-cni/config/cni
#  - name: netmon
#    release: silk
#  - name: vxlan-policy-agent
#    release: silk
#    properties:
#      ca_cert: ((network_policy_ca.certificate))
#      client_cert: ((network_policy_client.certificate))
#      client_key: ((network_policy_client.private_key))
#  - name: silk-daemon
#    release: silk
#    properties:
#      ca_cert: ((silk_ca.certificate))
#      client_cert: ((silk_daemon.certificate))
#      client_key: ((silk_daemon.private_key))
#  - name: silk-cni
#    release: silk
#    properties:
#      dns_servers:
#      - 169.254.0.2
- name: log-api
  azs:
  - z3
  - z2
  instances: 2
  vm_type: minimal
  stemcell: default
  update:
    serial: true
  networks:
  - name: default
  jobs:
  - name: loggregator_trafficcontroller
    release: loggregator
    consumes:
      doppler: {from: doppler}
    properties:
      uaa:
        internal_url: https://uaa.service.cf.internal:8443
        ca_cert: "((uaa_ca.certificate))"
      loggregator:
        tls:
          cc_trafficcontroller:
            cert: "((loggregator_tls_cc_tc.certificate))"
            key: "((loggregator_tls_cc_tc.private_key))"
          ca_cert: "((loggregator_ca.certificate))"
          trafficcontroller:
            cert: "((loggregator_tls_tc.certificate))"
            key: "((loggregator_tls_tc.private_key))"
        uaa:
          client_secret: "((uaa_clients_doppler_secret))"
      system_domain: "((system_domain))"
      ssl:
        skip_cert_verify: true
      cc:
        internal_service_hostname: "cloud-controller-ng.service.cf.internal"
        tls_port: 9023
        mutual_tls:
          ca_cert: "((service_cf_internal_ca.certificate))"
  - name: reverse_log_proxy
    release: loggregator
    provides:
      reverse_log_proxy: {as: reverse_log_proxy, shared: true}
    properties:
      loggregator:
        tls:
          ca_cert: "((loggregator_ca.certificate))"
          reverse_log_proxy:
            cert: "((loggregator_tls_rlp.certificate))"
            key: "((loggregator_tls_rlp.private_key))"
  - name: reverse_log_proxy_gateway
    release: loggregator
    properties:
      http:
        address: "0.0.0.0:8088"
      logs_provider:
        ca_cert: "((loggregator_ca.certificate))"
        client_cert: "((loggregator_rlp_gateway.certificate))"
        client_key: "((loggregator_rlp_gateway.private_key))"
      cc:
        capi_internal_addr: https://cloud-controller-ng.service.cf.internal:9023
        ca_cert: ((service_cf_internal_ca.certificate))
        cert: ((loggregator_rlp_gateway_tls_cc.certificate))
        key: ((loggregator_rlp_gateway_tls_cc.private_key))
        common_name: cloud-controller-ng.service.cf.internal
      uaa:
        ca_cert: ((uaa_ca.certificate))
        client_id: doppler
        client_secret: ((uaa_clients_doppler_secret))
        internal_addr: https://uaa.service.cf.internal:8443
  - name: route_registrar
    release: routing
    properties:
      route_registrar:
        routes:
        - name: loggregator
          port: 8080
          registration_interval: 20s
          uris:
          - loggregator.((system_domain))
        - name: doppler
          port: 8081
          registration_interval: 20s
          uris:
          - doppler.((system_domain))
          - "*.doppler.((system_domain))"
        - name: rlp-gateway
          port: 8088
          registration_interval: 20s
          uris:
          - log-stream.((system_domain))
          - "*.log-stream.((system_domain))"
- name: credhub
  azs: [z3, z2]
  instances: 2
  networks:
  - name: default
  stemcell: default
  vm_type: default 
  jobs:
  - name: credhub
    properties:
      credhub:
        authentication:
          mutual_tls:
            trusted_cas:
            - ((application_ca.certificate))
          uaa:
            ca_certs:
            - ((uaa_ca.certificate))
            url: https://uaa.service.cf.internal:8443
            verification_key: ((uaa_jwt_signing_key.public_key))
        authorization:
          acls:
            enabled: true
          permissions:
          - path: /*
            actors: ["uaa-client:credhub_admin_client"]
            operations: [read, write, delete, read_acl, write_acl]
          - path: /*
            actors: ["uaa-client:cc_service_key_client"]
            operations: [read]
        data_storage:
          database: credhub
          host: sql-db.service.cf.internal
          password: ((credhub_database_password))
          port: 3306
          require_tls: false
          type: mysql
          username: credhub
        encryption:
          keys:
          - active: true
            key_properties:
              encryption_password: ((credhub_encryption_password))
            provider_name: internal-provider
          providers:
          - name: internal-provider
            type: internal
        tls: ((credhub_tls))
    release: credhub
- name: rotate-cc-database-key
  instances: 1
  azs: [z1]
  lifecycle: errand
  vm_type: minimal
  stemcell: default
  networks:
  - name: default
  jobs:
  - name: rotate_cc_database_key
    release: capi
    properties: {}

variables:
- name: blobstore_admin_users_password
  type: password
- name: blobstore_secure_link_secret
  type: password
- name: cc_bulk_api_password
  type: password
- name: cc_db_encryption_key
  type: password
- name: cc_internal_api_password
  type: password
- name: cc_staging_upload_password
  type: password
- name: cf_mysql_mysql_admin_password
  type: password
- name: cf_mysql_mysql_cluster_health_password
  type: password
- name: cf_mysql_mysql_galera_healthcheck_endpoint_password
  type: password
- name: cf_mysql_mysql_galera_healthcheck_password
  type: password
- name: cf_mysql_proxy_api_password
  type: password
- name: cc_database_password
  type: password
- name: credhub_database_password
  type: password
- name: diego_database_password
  type: password
- name: uaa_database_password
  type: password
- name: routing_api_database_password
  type: password
- name: network_policy_database_password
  type: password
- name: network_connectivity_database_password
  type: password
- name: uaa_default_encryption_passphrase
  type: password
- name: silk_ca
  type: certificate
  options:
    is_ca: true
    common_name: silk-ca
    duration: ((cert_days))
- name: silk_controller
  type: certificate
  options:
    ca: silk_ca
    common_name: silk-controller.service.cf.internal
    extended_key_usage:
    - server_auth
    duration: ((cert_days))
- name: silk_daemon
  type: certificate
  options:
    ca: silk_ca
    common_name: silk-daemon
    extended_key_usage:
    - client_auth
    duration: ((cert_days))
- name: network_policy_ca
  type: certificate
  options:
    is_ca: true
    common_name: networkPolicyCA
    duration: ((cert_days))
- name: network_policy_server
  type: certificate
  options:
    ca: network_policy_ca
    common_name: policy-server.service.cf.internal
    extended_key_usage:
    - server_auth
    duration: ((cert_days))
- name: network_policy_client
  type: certificate
  options:
    ca: network_policy_ca
    common_name: clientName
    extended_key_usage:
    - client_auth
    duration: ((cert_days))
- name: uaa_clients_routing_api_client_secret
  type: password
- name: uaa_clients_tcp_emitter_secret
  type: password
- name: nats_password
  type: password
- name: router_status_password
  type: password
- name: cf_admin_password
  type: password
- name: cf_bosh_password
  type: password
- name: router_route_services_secret
  type: password
- name: uaa_admin_client_secret
  type: password
- name: uaa_clients_cc-routing_secret
  type: password
- name: uaa_clients_cc-service-dashboards_secret
  type: password
- name: uaa_clients_cc_service_key_client_secret
  type: password
- name: uaa_clients_cloud_controller_username_lookup_secret
  type: password
- name: uaa_clients_doppler_secret
  type: password
- name: uaa_clients_gorouter_secret
  type: password
- name: uaa_clients_network_policy_secret
  type: password
- name: uaa_clients_ssh-proxy_secret
  type: password
- name: uaa_clients_tcp_router_secret
  type: password
- name: diego_bbs_encryption_keys_passphrase
  type: password
- name: credhub_encryption_password
  type: password
- name: credhub_admin_client_secret
  type: password
- name: diego_ssh_proxy_host_key
  type: ssh
- name: uaa_jwt_signing_key
  type: rsa
- name: service_cf_internal_ca
  type: certificate
  options:
    is_ca: true
    common_name: internalCA
    duration: ((cert_days))
- name: blobstore_tls
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: blobstore.service.cf.internal
    duration: ((cert_days))
- name: diego_auctioneer_client
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: auctioneer client
    extended_key_usage:
    - client_auth
    duration: ((cert_days))
- name: diego_auctioneer_server
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: auctioneer.service.cf.internal
    extended_key_usage:
    - server_auth
    alternative_names:
    - "*.auctioneer.service.cf.internal"
    - auctioneer.service.cf.internal
    duration: ((cert_days))
- name: diego_bbs_client
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: bbs client
    extended_key_usage:
    - client_auth
    duration: ((cert_days))
- name: diego_bbs_server
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: bbs.service.cf.internal
    extended_key_usage:
    - server_auth
    - client_auth
    alternative_names:
    - "*.bbs.service.cf.internal"
    - bbs.service.cf.internal
    duration: ((cert_days))
- name: diego_rep_client
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: rep client
    extended_key_usage:
    - client_auth
    duration: ((cert_days))
- name: diego_rep_agent_v2
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: cell.service.cf.internal
    extended_key_usage:
    - client_auth
    - server_auth
    alternative_names:
    - "*.cell.service.cf.internal"
    - cell.service.cf.internal
    - 127.0.0.1
    - localhost
    duration: ((cert_days))
- name: loggregator_ca
  type: certificate
  options:
    is_ca: true
    common_name: loggregatorCA
    duration: ((cert_days))
- name: loggregator_tls_statsdinjector
  type: certificate
  options:
    ca: loggregator_ca
    common_name: statsdinjector
    extended_key_usage:
    - client_auth
    duration: ((cert_days))
- name: loggregator_tls_agent
  type: certificate
  options:
    ca: loggregator_ca
    common_name: metron
    extended_key_usage:
    - client_auth
    - server_auth
    duration: ((cert_days))
- name: loggregator_tls_doppler
  type: certificate
  options:
    ca: loggregator_ca
    common_name: doppler
    extended_key_usage:
    - client_auth
    - server_auth
    duration: ((cert_days))
- name: loggregator_tls_tc
  type: certificate
  options:
    ca: loggregator_ca
    common_name: trafficcontroller
    extended_key_usage:
    - client_auth
    - server_auth
    duration: ((cert_days))
- name: loggregator_tls_cc_tc
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: trafficcontroller
    extended_key_usage:
    - client_auth
    duration: ((cert_days))
- name: loggregator_rlp_gateway_tls_cc
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: rlp-gateway
    extended_key_usage:
    - client_auth
    duration: ((cert_days))
- name: loggregator_tls_rlp
  type: certificate
  options:
    ca: loggregator_ca
    common_name: reverselogproxy
    extended_key_usage:
    - client_auth
    - server_auth
    duration: ((cert_days))
- name: loggregator_rlp_gateway
  type: certificate
  options:
    ca: loggregator_ca
    common_name: rlp_gateway
    extended_key_usage:
    - client_auth
    duration: ((cert_days))
- name: adapter_rlp_tls
  type: certificate
  options:
    ca: loggregator_ca
    common_name: ss-adapter-rlp
    extended_key_usage:
    - client_auth
    - server_auth
    duration: ((cert_days))
- name: scheduler_api_tls
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: ss-scheduler
    extended_key_usage:
    - client_auth
    - server_auth
    duration: ((cert_days))
- name: adapter_tls
  type: certificate
  options:
    ca: loggregator_ca
    common_name: ss-adapter
    extended_key_usage:
    - server_auth
    - client_auth
    duration: ((cert_days))
- name: scheduler_client_tls
  type: certificate
  options:
    ca: loggregator_ca
    common_name: ss-scheduler
    extended_key_usage:
    - client_auth
    duration: ((cert_days))
- name: logs_provider
  options:
    ca: loggregator_ca
    common_name: log-cache
    extended_key_usage:
    - client_auth
    - server_auth
    duration: ((cert_days))
  type: certificate
- name: log_cache_ca
  options:
    common_name: log-cache
    is_ca: true
    duration: ((cert_days))
  type: certificate
- name: log_cache
  options:
    alternative_names:
    - log_cache
    - log-cache
    - logcache
    ca: log_cache_ca
    common_name: log-cache
    extended_key_usage:
    - client_auth
    - server_auth
    duration: ((cert_days))
  type: certificate
- name: log_cache_tls_cc_auth_proxy
  options:
    ca: service_cf_internal_ca
    common_name: log-cache
    extended_key_usage:
    - client_auth
    duration: ((cert_days))
  type: certificate
- name: router_ca
  type: certificate
  options:
    is_ca: true
    common_name: routerCA
    duration: ((cert_days))
- name: router_ssl
  type: certificate
  options:
    ca: router_ca
    common_name: routerSSL
    alternative_names:
    - "((system_domain))"
    - "*.((system_domain))"
    duration: ((cert_days))
- name: uaa_ca
  type: certificate
  options:
    is_ca: true
    common_name: uaaCA
    duration: ((cert_days))
- name: uaa_ssl
  type: certificate
  options:
    ca: uaa_ca
    common_name: uaa.service.cf.internal
    alternative_names:
    - uaa.service.cf.internal
    duration: ((cert_days))
- name: uaa_login_saml
  type: certificate
  options:
    ca: uaa_ca
    common_name: uaa_login_saml
    duration: ((cert_days))
- name: cc_tls
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: cloud-controller-ng.service.cf.internal
    extended_key_usage:
    - client_auth
    - server_auth
    duration: ((cert_days))
- name: cc_public_tls
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: "api.((system_domain))"
    alternative_names:
    - "api.((system_domain))"
    - cloud-controller-ng.service.cf.internal
    duration: ((cert_days))
- name: cc_bridge_tps
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: tps_watcher
    extended_key_usage:
    - client_auth
    duration: ((cert_days))
- name: cc_bridge_cc_uploader
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: cc_uploader
    extended_key_usage:
    - client_auth
    duration: ((cert_days))
- name: cc_bridge_cc_uploader_server
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: cc-uploader.service.cf.internal
    extended_key_usage:
    - server_auth
    duration: ((cert_days))
- name: diego_locket_server
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: locket.service.cf.internal
    extended_key_usage:
    - server_auth
    alternative_names:
    - "*.locket.service.cf.internal"
    - locket.service.cf.internal
    duration: ((cert_days))
- name: diego_locket_client
  type: certificate
  options:
    ca: service_cf_internal_ca
    common_name: locket client
    extended_key_usage:
    - client_auth
    duration: ((cert_days))
- name: locket_database_password
  type: password
- name: application_ca
  type: certificate
  options:
    common_name: appRootCA
    is_ca: true
    duration: ((cert_days))
- name: diego_instance_identity_ca
  type: certificate
  options:
    ca: application_ca
    common_name: instanceIdentityCA
    is_ca: true
    duration: ((cert_days))
- name: gorouter_backend_tls
  type: certificate
  options:
    ca: service_cf_internal_ca
    extended_key_usage:
    - client_auth
    common_name: gorouter_backend_tls
    alternative_names:
    - gorouter.service.cf.internal
    duration: ((cert_days))
- name: credhub_ca
  type: certificate
  options:
    common_name: credhubServerCa
    is_ca: true
    duration: ((cert_days))
- name: credhub_tls
  type: certificate
  options:
    ca: credhub_ca
    common_name: credhub.((system_domain))
    alternative_names:
    - credhub.service.cf.internal
    - credhub.((system_domain))
    duration: ((cert_days))

releases:
- name: binary-buildpack
  url: https://bosh.io/d/github.com/cloudfoundry/binary-buildpack-release?v=1.0.27
  version: 1.0.27
  sha1: f0022e0632d31d5bbb0e0a5e424244bad83158a1
- name: bpm
  url: https://bosh.io/d/github.com/cloudfoundry-incubator/bpm-release?v=0.13.0
  version: 0.13.0
  sha1: 4b6ebfdaa467c04855528172b099e565d679e0f5
- name: capi
  url: https://bosh.io/d/github.com/cloudfoundry/capi-release?v=1.71.0
  version: 1.71.0
  sha1: cae78eb73e32e4b86eeda994a3de88ac9414f346
- name: cf-mysql
  url: https://bosh.io/d/github.com/cloudfoundry/cf-mysql-release?v=36.15.0
  version: 36.15.0
  sha1: 0764d9d6aae7cefd10019437ed83e7715e614633
- name: cf-networking
  url: https://bosh.io/d/github.com/cloudfoundry/cf-networking-release?v=2.17.0
  version: 2.17.0
  sha1: 0088d75912db87c28084347dc488370a9aa9fad0
- name: cf-smoke-tests
  url: https://bosh.io/d/github.com/cloudfoundry/cf-smoke-tests-release?v=40.0.13
  version: 40.0.13
  sha1: feb38476ae15ec861f542de2f015dbb50b84d918
- name: cf-syslog-drain
  url: https://bosh.io/d/github.com/cloudfoundry/cf-syslog-drain-release?v=8.0
  version: "8.0"
  sha1: 5b5a2ea2f7a75755e19620eea0672a21704b0660
- name: cflinuxfs2
  url: https://bosh.io/d/github.com/cloudfoundry/cflinuxfs2-release?v=1.244.0
  version: 1.244.0
  sha1: 67f473bb1296b1faf598bceef2a36e9584b510f0
- name: credhub
  url: https://bosh.io/d/github.com/pivotal-cf/credhub-release?v=2.1.1
  version: 2.1.1
  sha1: dd0b607d855a8d23e6fd52b990573570e68bbbf6
- name: diego
  url: https://bosh.io/d/github.com/cloudfoundry/diego-release?v=2.20.0
  version: 2.20.0
  sha1: 493e365cbd24f7bb480ea5eca4906d258575617b
- name: dotnet-core-buildpack
  url: https://bosh.io/d/github.com/cloudfoundry/dotnet-core-buildpack-release?v=2.1.5
  version: 2.1.5
  sha1: 188dace870c94749a3abc21cb538c08e0f4f3d8c
- name: garden-runc
  url: https://bosh.io/d/github.com/cloudfoundry/garden-runc-release?v=1.16.8
  version: 1.16.8
  sha1: 67e6b474d63b402f725ec2d298fd959c9cd8d7c7
- name: go-buildpack
  url: https://bosh.io/d/github.com/cloudfoundry/go-buildpack-release?v=1.8.28
  version: 1.8.28
  sha1: cde33a1eda5392dd5c887d4d72568aabefb1f20a
- name: java-buildpack
  url: https://bosh.io/d/github.com/cloudfoundry/java-buildpack-release?v=4.16.1
  version: 4.16.1
  sha1: 2e24d3da499fc62fd3f012f13188e7e7e7857c2a
- name: loggregator
  url: https://bosh.io/d/github.com/cloudfoundry/loggregator-release?v=104.0
  version: "104.0"
  sha1: fc9f722ab0d321427942ed36dae70a49f084f9cf
- name: nats
  url: https://bosh.io/d/github.com/cloudfoundry/nats-release?v=26
  version: "26"
  sha1: 2f2c27acdfff81f3519968921686522518ab5783
- name: nodejs-buildpack
  url: https://bosh.io/d/github.com/cloudfoundry/nodejs-buildpack-release?v=1.6.32
  version: 1.6.32
  sha1: 69c02ed7bfcc145447222b7366d6ad41c07d86e5
- name: php-buildpack
  url: https://bosh.io/d/github.com/cloudfoundry/php-buildpack-release?v=4.3.61
  version: 4.3.61
  sha1: 9c5f9f040135fda81da44c6e1b0a1da878b04bfb
- name: python-buildpack
  url: https://bosh.io/d/github.com/cloudfoundry/python-buildpack-release?v=1.6.21
  version: 1.6.21
  sha1: 03b8618c5940889529e0aa6c129436e17c2a28d3
- name: routing
  url: https://bosh.io/d/github.com/cloudfoundry-incubator/cf-routing-release?v=0.182.0
  version: 0.182.0
  sha1: 6e341a527a0dd2c920b54dea82156ae9bc0b29eb
- name: ruby-buildpack
  url: https://bosh.io/d/github.com/cloudfoundry/ruby-buildpack-release?v=1.7.24
  version: 1.7.24
  sha1: d696be61e29dd0ea60129df148747831feece51f
- name: silk
  url: https://bosh.io/d/github.com/cloudfoundry/silk-release?v=2.17.0
  version: 2.17.0
  sha1: 752a269d854ca3840b1ec5f6d664efb3901e73e5
- name: staticfile-buildpack
  url: https://bosh.io/d/github.com/cloudfoundry/staticfile-buildpack-release?v=1.4.32
  version: 1.4.32
  sha1: 5851241b881f3315383fe4d2a50ca89fe2a5d331
- name: statsd-injector
  url: https://bosh.io/d/github.com/cloudfoundry/statsd-injector-release?v=1.5.0
  version: 1.5.0
  sha1: 49a38aca12e0fd2ec28f96c780c1971427dda04e
- name: uaa
  url: https://bosh.io/d/github.com/cloudfoundry/uaa-release?v=64.0
  version: "64.0"
  sha1: f10caefdd136675975bfc88d6409f322c68209d4
- name: loggregator-agent
  url: https://bosh.io/d/github.com/cloudfoundry/loggregator-agent-release?v=2.3
  version: "2.3"
  sha1: 7532818b5d518b87b57cf36ddc2bf81758f606f9
- name: log-cache
  url: https://bosh.io/d/github.com/cloudfoundry/log-cache-release?v=2.0.1
  version: 2.0.1
  sha1: 23f9c4767109293b5f8b8a6a07ca31f065bcf88b
- name: bosh-dns-aliases
  url: https://bosh.io/d/github.com/cloudfoundry/bosh-dns-aliases-release?v=0.0.3
  version: 0.0.3
  sha1: b0d0a0350ed87f1ded58b2ebb469acea0e026ccc
- name: cf-cli
  url: https://bosh.io/d/github.com/bosh-packages/cf-cli-release?v=1.9.0
  version: 1.9.0
  sha1: 9dfcb2ffec5abb77ea9bd4561655a8444405c481
stemcells:
- alias: default
  os: ubuntu-xenial
  version: "97.28"
