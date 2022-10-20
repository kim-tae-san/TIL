# Postgresql install apt error 

waiting for cache lock could not get lock /var/lib/dpkg/lock-frontend

solved !! 

=> 
sudo rm /var/lib/apt/lists/lock 
sudo rm /var/cache/apt/archives/lock
sudo rm /var/lib/dpkg/lock*

# Patroni, etcd, haproxy ssh connection error

-> https://lee-automation-lab.tistory.com/entry/SSH-%EC%A0%91%EC%86%8D-%EC%8B%9C-Disconnected-No-supported-authentication-methods-available-server-sent-publickeygssapi-keyexgssapi-with-mic