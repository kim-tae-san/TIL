# Keepalived

HAProxy의 이중화를 위한 툴

> 참고한 자료 

https://www.peternijssen.nl/high-availability-haproxy-keepalived-aws/


> errors 

    1. WARNING - default user 'keepalived_script' for script execution does not exist - please create.
    https://github.com/hap-wi/roxy-wi/issues/224

    2.  killall5[26466]: only one argument, a signal number, allowed

    -> vrrp script를 pidof에서 pgrep으로 바꾸어줌
    https://github.com/Oefenweb/ansible-keepalived/issues/29


# AWS cli

-> 공식홈페이지에서 설치 후 configure 명령어를 통해 인증

> IAM 생성 : Access Key 방식으로

    elastic ip에 대한 접근권한을 준 다음 생성된 user의 key와 password를 따로 저장하여 haproxy가 실행되는 VM에서 aws configure로 로그인 

    ! 주의사항: region을 다르게 설정하면 timezone이 맞지않아 자격증명오류발생

    ! master.sh 주의사항
    -> 권한이 root인 경우 $home 상대위치가 변경되어 .aws/config 등을 /root에서 찾게된다. unable to locate credential error가 발생할 경우 이 부분을 확인 하길.

    나의 해결방법: 계정을 root로 바꿔서 aws configure를 다시 해주어 root 디렉토리에 config를 생성


    https://potato-yong.tistory.com/94

    https://stackoverflow.com/questions/31425838/bash-with-aws-cli-unable-to-locate-credentials

