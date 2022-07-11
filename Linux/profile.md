# etc/profile 과  /.profile 의 차이점

    유닉스의 /etc/profile 과 /.profile은 모두 사용자가 로그인했을때 적용되는 스크립트를 정의해놓은 파일이다. 도스에서 부팅할때마다 실행된 autoexec.bat 파일과 같은 맥락이다.    사용자가 로그인할 경우 /etc/profile과 각 사용자의 .profile 스크립트 내용이 실행된다.

 

    이렇게 같은 역할을 하지만 둘의 차이는 크다. /etc/profile가 모든 사용자에게 공통으로 적용되는 내용을 담고 있다면,     .profile의 경우 해당 사용자에 한해 사용되는 특징이 있다. 각 사용자의 .profile은 보통 /home/userid/.profile로 위치하며 root 사용자의 경우에는 /root/.profile로 위치하게 된다. 단, /root/.profile의 경우 이 파일은 root 사용자에게만 적용되며, 이 파일을 수정했다고 해서 전체 사용자에 적용되지 않으니 유의해야 한다.