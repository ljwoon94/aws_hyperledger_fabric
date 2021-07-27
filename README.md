# 1. 개요
AWS EC2와 docker swarm을 사용해, amazon managed blockchain 없이 하이퍼레저 패브릭 설치 및 raft 오더러 서비스 사용

# 2. 네트워크 구성

RAFT 오더링 서비스를 사용하기 위해, AWS EC2에서 인스턴스 5개 생성.
각 인스턴스는 블록체인 노드가 된다. 

NODE1 : Orderer1, CA, peer0.org1, msp, couchdb, CLI 웹 클라이언트 서버 
NODE2 : Orderer2, CA, peer0.org2, msp, couchdb, 웹 서버 
NODE3 : Orderer3, CA, peer0.org3, msp, couchdb
NODE4 : Orderer4, CA, peer0.org4, msp, couchdb
NODE5 : Orderer5, CA, peer0.org5, msp, couchdb

5개의 조직 전부 한 채널에 들어갈 예정



# 3. 환경설정

## 3.1 AWS EC2서버 생성

AWS EC2로 접속 후 인스턴스 시작

![image](https://user-images.githubusercontent.com/68358404/127076627-1cccaeb3-f67f-450c-b281-daecc84bc868.png)

Ubuntu Server 20.04 LTS 버전 클릭

![image](https://user-images.githubusercontent.com/68358404/127076676-d00b870a-2f9e-4459-889a-b08a69e7ea79.png)

t2.micro 선택 후 다음 검토 및 시작

![image](https://user-images.githubusercontent.com/68358404/127077173-1bec513e-0ffa-4cb4-8f55-afb8ed175574.png)

시작하기 클릭.

![image](https://user-images.githubusercontent.com/68358404/127077280-14295dfd-166d-4a89-8d1c-74a9cd8c97d5.png)

기존 키가 있는 사람은 그대로 사용하고, 새 키가 필요한 사람은 새 키 패어 생성에서 이름을 설정하고 키 페어를 다운로드 한다.
키 페어는 매우 중요함으로, 잘 보관해야하고 절대로 github에 올리는 실수를 하지 말아야한다. 아니면 aws 계정 정지 당한다.

![image](https://user-images.githubusercontent.com/68358404/127077390-fa2d6a1a-5756-4e11-84a0-794e3168ba28.png)

이런 식으로 EC2 서버 다섯개 생성

![image](https://user-images.githubusercontent.com/68358404/127077804-0e9f77c4-5d8e-44d1-b982-7fbbb79c64d3.png)

## 3.1 EC2서버 인바운드 규칙

지금 만들어둔 서버들은 모든 아이피, 포트번호로 접속 가능하다. 인바운드에 규칙을 둬서 아무나 접속하지 못하게 설정한다.
인
네트워크 및 보안 탭에 보안 그룹 클릭

![image](https://user-images.githubusercontent.com/68358404/127078426-f66f9d2c-2f89-492d-ae1f-dd481bf5d319.png)

인바운드 규칙 수정

![image](https://user-images.githubusercontent.com/68358404/127078462-9fed3436-20f7-4afe-864c-9f4068c6c50b.png)

인바운드 규칙 편집

![image](https://user-images.githubusercontent.com/68358404/127078773-0e185b2b-5304-4a35-b61d-0db4949b9f70.png)

규칙의 의미

TCP port 2377: manager 노드에서 listen 하고 있는 포트(cluster management 통신에 사용)
TCP/UDP port 4789: Overlay Network 트래픽용 포트
TCP/UDP port 7946: 컨테이너 네트워크 검색을 위한 포트(Node 간 통신에 사용)


