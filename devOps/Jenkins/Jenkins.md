# Jenkins

> 커스터마이즈로 배포 간편화

    커스터마이즈는 야믈 파일에 정의된 값을 사용자가 원하는 값으로 변경할 수 있다.

    원래 쿠버네티스에서 오브젝트에 대한 수정 사항을 반영하려면 사용자가 직접 야믈 파일을 편집기 프로그램으로 수정해야한다. 

    하지만 야믈파일이 매우 많거나 하나의 야믈 파일로 환경이 다른 여러 개의 쿠버네티스 클러스터에 배포해야 해서 많은 노력이 든다. 

    커스터 마이즈는 이때 사용한다. 

    kustomize 명령을 사용하여서 kustomization.yaml이라는 기본 매니페스트를 만들고, 이 파일에 변경해야 하는 값들을 적용한다. 그러고 build 옵션으로 변경할 내용이 적용된 최종 야믈 파일을 저장하거나 변경된 내용이 바로 실행되도록 지정한다.


> 헬름으로 배포 간편화

    헬름에서는 차트를 이용해서 빌드한다. 해당 차트를만드는 개발자가 허브에 차트를 올리면 차트를 이용자들이 pull해와서 사용하는 맥락이다.


> 헬름으로 젠킨스 설치

    nfs 바인딩
    ~/ ...  /nfs-exporter.sh jenkins


> 젠킨스 컨트롤러 ID

    젠킨스 컨트롤러 설치 후에 내부 유저 ID와 그룹 ID를 보면 1000번으로 설정되어 있는 것을 확인할 수 있다.
    
    따라서 PV로 사용되는 NFS 디렉터리의 근접 ID를 동일하게 설정하지 않으면 접근권한 에러가 뜬다. 젠킨스 설치 시 환경변수를 설정해서 해결하는 방법이 있지만 이는 수정할 것들이 많아서 간단히 호스트 시스템의 ID를 1000으로 수정해주는것으로 해결할 수 있다.

    chown 1000:1000 /nfs_shared/jenkins/
    ls -n /nfs_shared


> master node의 taints, tolerations

    taints는 매우 특별하게 취급돼야 하는 곳에 설정해 톨러레이션이라는 키로 접근 할 수 있게 한다.

    젠킨스는 쿠버네티스 마스터노드 위에 설치되었다.


> 젠킨스 PV, PVC 설정

    kubectl apply -f ~/ ... /jenkins-volume.yaml


> 젠킨스 서비스 어카운트를 위한 권한 설정하기

    kubectl create clusterrolebinding jenkins-cluster-admin --clusterrole=cluster-admin --serviceaccount=default:jenkins