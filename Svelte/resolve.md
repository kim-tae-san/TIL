# Svelte Dockerization

npm add @sveltejs/adapter-static -D --force로 static을 위한 어댑터 적용

>> 결과적으로 node 이미지를 사용하여 만들었고 multistage, node_module 일부삭제로 최적화

>> static을 사용하면 정적파일만 보낼 수 있는 대신 이미지가 줄어드는데 nginx프록시를 사용하면 API라우팅에 과부하 올 수 있음.