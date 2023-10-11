nginx 이미지를 빌드합니다.
1. index.html 파일을 수정
    <body>
        <h1>SUCCESS!</h1> => h1 사이의 문구를 바꿔요

2. jenkins에서 docker image를 만들고, 도커 허브에 push 합니다.
    docker build -t luckyjakey/nginx:v1 . 
    docker push luckyjakey/nginx:v1

    docker build -t $계정/$레포:$버전

3. yml파일은 쿠버네티스에 배포하는 파일
    변경할 경우 현재 디렉터리에서
    kubectl apply -f .

---------------------------

기본적으로 도커 이미지를 빌드해보기

1. index.html 변경해보기

2. docker build -t luckyjakey/nginx:v2(버전 변경해줘야함) . 
   docker push luckyjakey/nginx:v2(버전 변경해줘야함)

3. nginx-deploy.yaml 에서 image 명을 방금 빌드한 도커 이미지로 바꿔줍니다.
      image: luckyjakey/nginx:v1  >>>> image: luckyjakey/nginx:v2

4. 현재 경로에서 kubectl apply -f . 

5. kubectl get po => pod 떳는지 확인
   kubectl get svc => 서비스 ip & 포트 확인 

6. 서비스 ip 접속해서 변경된 거 확인
7. 과제 확인 및 업데이트


화이팅