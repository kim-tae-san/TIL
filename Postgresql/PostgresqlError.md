# ErrorList

> psql: could not connect to server: No such file or directory
    Is the server running locally and accepting
    connections on Unix domain socket "/var/run/postgresql/.s.PGSQL.5432"? 

- solved !
  
    sudo /etc/init.d/postgresql restart

- failed !
  
    다시 깔고 

    sudo pg_ctlcluster 14 main start

    sudo service postgresql restart