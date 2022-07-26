
# Docker Errorlist

> Postgresql 실행과정

    docker build -t postgres .

    docker run -d --name postgres -p 5555:5432 postgres


> Docker Compose 실행 과정 

    docker compose up -d

    docker-compose up --build -d


> Docker Compse 하면서 어려움을 겪었던 부분들

    postgresql 접속할때 @뒤에 localhost 말고 해당 db의 컨테이너 id 를 넣어줘야함 => 같은 네트워크끼리 컨테이너 이름으로 통신함

    db는 백그라운드 데몬으로 실행해줘야함 -d 요소 필수

    