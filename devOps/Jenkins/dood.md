# dood (docker out of docker)

젠킨스에서 dood로 docker agent를 실행하는 방법이다.

방법은 아예 도커를 컨테이너에서 독립적으로 띄우는게 아니라 Host docker의 docker.sock을 공유해서 docker out of docker 으로 띄우는 것이다.

