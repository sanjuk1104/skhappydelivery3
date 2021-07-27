# Azer Delivery
![image](https://user-images.githubusercontent.com/45377807/127104059-e6e79c97-a0fb-4153-9bf0-46251cb72683.png)


## 서비스 시나리오
### 아제르 배달 서비스

####기능적 요구사항
- 고객이 메뉴를 선택하여 주문한다
- 고객이 결제한다
- 결제가 되면 주문 및 결제 내역이 입점상점주인에게 전달된다
- 상점주인은 주문을 승인할 수 있다.
- 상점주인은 주문을 거절할 수 있다.
- 상점주인이 확인하여 주문을 접수하여 조리를 시작한다
- 조리가 끝나면 배달이 시작된다
- 고객이 주문을 취소할 수 있다
- 결제가 취소되면 상점에도 전달된다.
- 주문상태가 이벤트 발생시 마다 업데이트되어 View를 통해 보여진다

#### 비기능적 요구사항
##### 트랜잭션
- 주문이 되면 무조건 결제에 데이터가 저장된다. Sync 호출
- 상점주인이 주문을 승인하면 배달되어질 정보를 Order에서 가져와 저장한다.

##### 장애격리
- 상점관리 기능이 수행되지 않더라도 주문은 365일 24시간 받을 수 있어야 한다 Async (event-driven), Eventual Consistency
- 주문이 과도하게 생성되면 주문 생성을 잠시 지연하도록 유도한다 Circuit breaker, fallback

## 1. 분석/설계

### 이벤트스토밍

- url: http://www.msaez.io/#/storming/wf1WRjEyVVWd1Abldu2nsM6FwbL2/e7974643da20e8427e1f1943d1c115e9


### 이벤트 도출
![이벤트 추출](https://user-images.githubusercontent.com/45377807/127094277-43ca3827-c6a9-4621-a446-79f1794088b7.PNG)


### 액터, 커맨드 부착
![오더_2](https://user-images.githubusercontent.com/45377807/127094294-c972a961-dcf5-4472-927e-9ded9fa5742d.PNG)
![페이_2](https://user-images.githubusercontent.com/45377807/127094306-28726ddb-f624-499b-b3b6-d2bf6b6740e7.PNG)
![스토어_2](https://user-images.githubusercontent.com/45377807/127094316-27bcf99d-17ac-443d-bf40-dbc41a8facde.PNG)
![커스토머_2](https://user-images.githubusercontent.com/45377807/127094328-040ff6b7-eae0-4658-acfd-8d78b4c99100.PNG)
![딜리버리_2](https://user-images.githubusercontent.com/45377807/127094332-66874532-eebe-4c58-9c95-5b241bbdb83c.PNG)
 

### 폴리시 부착
![오더_1](https://user-images.githubusercontent.com/45377807/127093931-17f62510-6701-4b92-9e04-d451fc0d1780.PNG)
![페이_1](https://user-images.githubusercontent.com/45377807/127093943-b9f35d30-4d93-4ac7-9247-9a9037385e93.PNG)
![스토어_1](https://user-images.githubusercontent.com/45377807/127093954-62f7cf79-b6e8-42e6-a362-e9f3d10b053e.PNG)
![커스토머_1](https://user-images.githubusercontent.com/45377807/127093957-f7fce2f0-8b60-4684-a53d-171a6fd8ba56.PNG)
![딜리버리_1](https://user-images.githubusercontent.com/45377807/127093968-7706168b-50fb-45b9-a317-0c8f95ce5bb9.PNG)




### 어그리게잇 묶기
![오더](https://user-images.githubusercontent.com/45377807/127093725-8ce10916-137f-4bc4-a35e-d9a40e239aa3.PNG)
![페이](https://user-images.githubusercontent.com/45377807/127093732-39809f37-b972-41f9-8695-fa69275d2bdd.PNG)
![스토어](https://user-images.githubusercontent.com/45377807/127093743-44b16652-6018-4520-9d51-da7d46f2fc4b.PNG)
![카스터머](https://user-images.githubusercontent.com/45377807/127093753-deb105ad-2304-4481-ada9-99f09f29240b.PNG)
![딜리버리](https://user-images.githubusercontent.com/45377807/127093754-df33225c-2c2d-4bbf-bdcc-17b3c8fc0f20.PNG)



### 바운디드 컨텍스트 묶기
![바운디드컨텍스트 묶기](https://user-images.githubusercontent.com/45377807/127093523-bbc78fe0-7ee5-470d-be09-5c187948cdb6.PNG)




### 완성된 모형(실선은 Req/Res, 점선은 Pub/Sub)
![이벤트스토밍 결과](https://user-images.githubusercontent.com/45377807/127093331-338b2ab4-97ea-4fe9-a0c2-6bd9db916f9e.PNG)



### 헥사고날 아키텍처 다이어그램 도출
![헥사고날아키텍쳐](https://user-images.githubusercontent.com/45377807/127099006-d37f2eed-387c-4e11-87d3-2bc47609333e.png)

<br/>
<br/>


## 2. 구현

### 시나리오 흐름
1) 고객이 주문을 입력한다
![image](https://user-images.githubusercontent.com/45377807/127096412-076c6b9f-03c5-4298-8cba-112283577e56.png)

2) 주문에 대한 결제를 진행한다
![image](https://user-images.githubusercontent.com/45377807/127096540-ef6bb814-3d86-47df-9b96-b676e614d691.png)

3) 스토어 주인은 결제 완료된 주문을 접수하고 요리한다
![image](https://user-images.githubusercontent.com/45377807/127096623-12e469dc-26a5-49e5-a04f-be3e3913e6a4.png)

4) 고객은 커스터머 뷰를 통해 주문 상태 등을 확인한다
![image](https://user-images.githubusercontent.com/45377807/127097203-671b9c5e-070c-4c97-a718-9607ecf56aec.png)
![image](https://user-images.githubusercontent.com/45377807/127097279-d8e90f27-4969-465f-8786-36dd079e6a10.png)


5) 요리 완료된 음식은 배달이 된다
![image](https://user-images.githubusercontent.com/45377807/127097418-734eba3a-2eb4-41d2-8a04-fb903abcb797.png)


6) 고객은 주문을 취소할 수 있다
![image](https://user-images.githubusercontent.com/45377807/127097491-c19fdbc0-1d18-438f-b764-6de7bcd9401f.png)

### DDD 적용

|MSA|기능|포트|URL|
| :--: | :--: | :--: | :--: |
|Order|주문관리|8081|http://localhost:8081/orders/|
|Pay|결제관리|8082|http://localhost:8082/pays/|
|Store|주문접수 및 거절|8083|http://localhost:8083/stores/|
|Customer|고객관리|8084|http://localhost:8084/customers/|
|delivery|배달관리|8085|http://localhost:8085/deliveries/|


### Gateway 적용 
- Gateway > src> main > resources > application.yml

		server:
		  port: 8088

		---

		spring:
		  profiles: default
		  cloud:
		    gateway:
		      routes:
			- id: order
			  uri: http://localhost:8081
			  predicates:
			    - Path=/orders/** 
			- id: pay
			  uri: http://localhost:8082
			  predicates:
			    - Path=/pays/** 
			- id: store
			  uri: http://localhost:8083
			  predicates:
			    - Path=/stores/** 
			- id: customer
			  uri: http://localhost:8084
			  predicates:
			    - Path=/customers/** /myPages/**
			- id: delivery
			  uri: http://localhost:8085
			  predicates:
			    - Path=/deliveries/** 
		      globalcors:
			corsConfigurations:
			  '[/**]':
			    allowedOrigins:
			      - "*"
			    allowedMethods:
			      - "*"
			    allowedHeaders:
			      - "*"
			    allowCredentials: true


		---

		spring:
		  profiles: docker
		  cloud:
		    gateway:
		      routes:
			- id: order
			  uri: http://order:8080
			  predicates:
			    - Path=/orders/**
			- id: pay
			  uri: http://pay:8080
			  predicates:
			    - Path=/pays/**             
			- id: store
			  uri: http://store:8080
			  predicates:
			    - Path=/stores/** 
			- id: customer
			  uri: http://customer:8080
			  predicates:
			    - Path=/customers/** /myPages/**
			- id: delivery
			  uri: http://delivery:8080
			  predicates:
			    - Path=/deliveries/** 
		      globalcors:
			corsConfigurations:
			  '[/**]':
			    allowedOrigins:
			      - "*"
			    allowedMethods:
			      - "*"
			    allowedHeaders:
			      - "*"
			    allowCredentials: true

		server:
		  port: 8080

### 폴리글랏 퍼시스턴스
신규서비스인 Delivery 서비스만 DB를 구분 적용(DB 적용)

	<!--
			<dependency>
				<groupId>com.h2database</groupId>
				<artifactId>h2</artifactId>
				<scope>runtime</scope>
			</dependency>
	-->

			<dependency>
				<groupId>org.hsqldb</groupId>
				<artifactId>hsqldb</artifactId>
				<version>2.4.0</version>
				<scope>runtime</scope>
			</dependency>



### kafka 활용한 Pub/Sub 구조
![image](https://user-images.githubusercontent.com/45377807/127099630-08ae8efb-aa5f-4c1f-9993-473395432c37.png)
![image](https://user-images.githubusercontent.com/45377807/127100782-4039520f-0a9e-494c-9d4d-e23ab3ab358e.png)




## 3. 운영
### CI/CD 설정
- 각 서비스별 package, build, dockerhub push

	cd order 이동
	mvn package -B -Dmaven.test.skip=true #패키지
	docker build -t 879772956301.dkr.ecr.ap-southeast-2.amazonaws.com/user19-order:v2 . #도커 빌드
	docker push 879772956301.dkr.ecr.ap-southeast-2.amazonaws.com/user19-order:v2       #도커 푸쉬
	kubectl apply -f kubernetes/deployment.yml     #aws deploy 수행
	kubectl apply -f kubernetes/service.yam        #aws service 등록
![image](https://user-images.githubusercontent.com/45377807/127103384-4269617a-5795-41c8-93e1-ed344403fb0b.png)
![image](https://user-images.githubusercontent.com/45377807/127106694-4ae09463-a432-412e-b79d-5c025a3b32d3.png)


- 최종 Deploy완료
![image](https://user-images.githubusercontent.com/45377807/127104411-ec4ed780-5a19-4ae9-9481-2f7965099c56.png)


### Pod생성 시 Liveness 와 Readiness Probe를 적용했는가?
##### Zero-downtime deploy(Readiness Probe 적용)
- order deployment.yml

		  readinessProbe:
		    httpGet:
		      path: '/actuator/health'
		      port: 8080
		    initialDelaySeconds: 10
		    timeoutSeconds: 2
		    periodSeconds: 5
		    failureThreshold: 10

#### Self Healing(Liveness Probe 적용) 
- order deployment.yml

		livenessProbe:
		    httpGet:
		      path: '/actuator/health'
		      port: 8080
		    initialDelaySeconds: 120
		    timeoutSeconds: 2
		    periodSeconds: 5
		    failureThreshold: 5






