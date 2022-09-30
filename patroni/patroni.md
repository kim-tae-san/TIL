# Patroni

> Patroni란

    HA를 위한 daemon 서비스이다.

    Patroni를 사용하면 PostgreSQL의 start/stop/replication을 제어할 수 있고, failover를 자동화 할 수 있고, management API를 제공한다.

! failover란

DB시스템에 장애가 생겨 replica서버에서 DB작업을 수행하는것 이중화에 필수적임

> Patroni의 특징

1. Simplicity
2. Usability
3. Availability

> Simplicity goals

* One container image
* Rely on Kubernetes
* "just works" defaults

> Useability 

* Management port(8008)
* Configuration & plugins
* No limitations on Postgres

> Availability

* Fully automated replication and failover

