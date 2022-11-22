# Unable to connect to the server: x509: certificate has expired or is not yet valid

/etc/kubernetes/pki 에서

> 쿠버네티스 인증서 만료 오류

    cp -rf /etc/kubernetes/pki /etc/kubernetes/pki-backup
    위 명령어를 통해 백업폴더 생성

    sudo kubeadm certs renew all 로 갱신 성공

/etc/kubernetes 에서

config 파일 백업하고, config 파일 생성
    
    백업파일 생성
    $ cd /etc/kubernetes
    $ sudo mkdir -p backup
    $ sudo mv admin.conf backup/admin.conf.old
    $ sudo mv kubelet.conf backup/kubelet.conf.old
    $ sudo mv controller-manager.conf backup/controller-manager.conf.old
    $ sudo mv scheduler.conf backup/scheduler.conf.old

    신규파일 생성
    $ sudo kubeadm init phase kubeconfig admin
    $ sudo kubeadm init phase kubeconfig kubelet
    $ sudo kubeadm init phase kubeconfig controller-manager
    $ sudo kubeadm init phase kubeconfig scheduler

    kubernetes 컴포넌트 재시작
    $ sudo kill -s SIGHUP $(pidof kube-apiserver)
    $ sudo kill -s SIGHUP $(pidof kube-controller-manager)
    $ sudo kill -s SIGHUP $(pidof kube-scheduler)
    $ sudo systemctl restart kubelet


!! 주의사항
> error: You must be logged in to the server (Unauthorized) 의 경우

    $ mkdir -p $HOME/.kube
    $ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
    $ sudo chown $(id -u):$(id -g) $HOME/.kube/config

참조 : https://danawalab.github.io/kubernetes/2022/03/28/Renew-Kubernates.html

https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-certs/

https://gihyun.com/133