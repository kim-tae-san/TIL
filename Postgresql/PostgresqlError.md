# ErrorList

> psql: could not connect to server: No such file or directory
    Is the server running locally and accepting
    connections on Unix domain socket "/var/run/postgresql/.s.PGSQL.5432"? 

- solved !
  
    sudo /etc/init.d/postgresql restart

- failed !
  
    다시 깔고 

    sudo pg_ctlcluster 14 main start

     


> Docker 문제

    psql: error: connection to server on socket "/var/run/postgresql/.s.PGSQL.5432" failed: FATAL:  role "root" does not exist

    psql --username=postgres --dbname=postgres

    위 명령어로 유저 변경해 준 다음에 접속하였음.