# 서버 구성

## WEV/WAS 서버
- Accordian 환경으로 구성. 아래 서버설정 메뉴 및 관련 운영매뉴얼 참고.

## DB 서버(Oracle)

### [eBiz DB]
-개발 172.18.2.204:1521:DEV01 smpt/ptsm
-운영 172.18.2.10:1521:MIS10 smpt/ptsm

### [오일뱅크비즈 DB]
- 개발 172.18.200.205:1521:DEV02DBS club/obcl
- 운영 172.18.2.210:1521:MIS05DBS club/obcl

### [카드DB]
- 운영 (개발없음) 172.18.2.13:1521:dwcard01 smbz/BZDV

### [쿠폰DB]
- 개발 172.18.2.17:1521:DEV03 smsc/scsm
- 운영 172.18.2.16:1521:MIS11 smsc/scsm

### [상품권DB]
- 개발 172.18.2.204:1521:DEV01 smsp/spdv
- 운영 172.18.2.10:1521:MIS10 smsp/spdv


# 외부 솔루션 및 라이브러리 관련

## RealGrid2

- 라이선스 신청
  - 개발용 : realgrid.com 홈페이지에서 다운로드, 1개월단위 갱신, 
  - 신청도메인 : localhost,127.0.0.1,devhigis.oilbank.co.kr,higis.oilbank.co.kr,172.18.200.78,172.18.61.76,172.18.61.77
  - 운영용 :  주명관 상무님 확인. 리얼그리드 윤대섭 전무이사, 010-6324-4761, dsyoon@realgrid.com

- 라이선스 위치
  - src\main\webapp\resources\plugin\realgrid2 하위 개발환경별 디렉토리(local,dev,prod)에 구분해서 복사
  - header.jsp 에서 런타입환경 구분변수로 라이선스 파일 처리
    - ```javascript
      <script type="text/javascript" src="/resources/plugin/realgrid2/<%=runtimeMode%>/realgrid-lic.js"></script>
      ```

## DEXT5Upload

- http://www.dext5.com/ 에서 임시라이선스 발급
	* 사용도메인 : localhost,devebiz.oilbank.co.kr
- 라이선스 등록
 	* src\main\webapp\resources\plugin\dext5upload\config 디렉토리에 웹/모바일, 로컬/개발/운영 환경별로 파일 구분
- 업로드 처리 핸들러(커스트마이징)
	* higis\src\main\webapp\plugin\dext5handler.jsp
- 업로드파일 DB 저장 Service
	* net.ecbank.fwk.common.service.DEXT5UploadService.java
- 정식라이선스 담당자
	* 정유희 : yhlove@raonwiz.com, 02-584-3992
- 정식 라이선스는 관리자 제한용 임.
	* dextupload를 사용하는 화면의 url을 정식 라이선스 요청 시 등록해야 됨.
	* 유지보수기간 내에는 추가 url 요청하고 갱신할 수 있음.  
    
# 로컬 개발환경

## 이클립스 설정

### Tomcat Hot deploy 관련 설정

	* server options : 모두 해제
	* publishing : automatically pubblish when resource change 옵션 선택
	               update context paths 선택
	* Web Module edit 팝업에서 Auto realoging context 체크해제

### 클래스 기본 주석 comment 설정

	* Window > Preperence > java > code style > code template
	  Comments > Types 에 아래와 같이 설정.
	
	  /**
		 * 클래스명
		 *
		 * @author ${user}
		 * @since ${date}
		 * @version 1.0
		 * @see
		 *
		 * <pre>
		 * << 상세설명 >>
		 *
		 * << 개정이력(Modification Information) >>
		 * ${date} ${user} - 최초 작성.
		 * </pre>
	    */

### JSP 파일 기본 tempate 주석 설정

	* Window > Preperence > Web > Jsp file > editor > template > new jsp file (HTML5) 에 아래와 같이 설정.
	
	<%--
	설  명 :
	작성일 : ${date}
	작성자 : ${user}
	수정일 		수정자 		수정내용
	------- -------- ---------------------------
	${date} 	${user} 	최초 생성
	--%>
	<%@ page language="java" contentType="text/html; charset=${encoding}" pageEncoding="${encoding}"%>
	<%@ taglib prefix="c"      uri="http://java.sun.com/jsp/jstl/core" %>
	<%@ taglib prefix="fn"     uri="http://java.sun.com/jsp/jstl/functions" %>
	<%@ taglib prefix="fmt"    uri="http://java.sun.com/jsp/jstl/fmt" %>
	<%@ taglib prefix="spring" uri="http://www.springframework.org/tags" %>
	<%@ taglib prefix="sec"    uri="http://www.springframework.org/security/tags" %>
	<!DOCTYPE html>
	<html>
	<head>
	<meta charset="${encoding}">
	<title>Insert title here</title>
	</head>
	<body>
	${cursor}
	</body>
	</html>

# Accordion 개발서버 설정

URL : https://172.18.21.146:30000 (accuser02e // o*******!)

## 전체 요약

- 인프라보안사무국에 개발서버 기본환경 설정 요청
- 노드는 1개로 Web / Was 각각의 Pod로 요청
- IP 및 외부 접근 Port는 인프라보안사무국에서 설정
- NAS Mount가 필요한 경우 볼륨 추가 요청 및 mount 디렉토리 정보만 전달

## 상세

1. 프로젝트 대시보드

|          이름          |      노드        |   Pod IP      | 컨테이너 |
| --------------------- | -------------- | ------------- | ----- |
| ebiz-dev-web          | cpnodeintdev1  | XXX.XX.X.XX   |  1/1  |
| ebiz-dev-was          | cpnodeintdev1  | XXX.XX.X.XX   |  2/2  |

```
위 테이블과 같이 포드 영역에 생성 필요
Pod IP는 자동 생성
```



2. 앱

> **ebiz-dev-web** 

- Service Type: NodePort

- Ports

|    Name   | Container Port | Protocol | Node Port | Service Port |
| --------- | -------------- | -------- | --------- | ------------ |
| http-port | 80             | TCP      | xxxxx     | 80           |

- Volumes

|       볼륨명       | 볼륨마운트 경로            | 파일마운트(Optional) |
| ---------------- | ---------------------- | ----------------- |
| ebiz-dev-log-web | .. /logs               |                   |
| ebiz-dev-upload  | /data                  |                   |
| ebiz-dev-ebook   | .. /htdocs/ebiz/ebook  |                   |

>> ※**볼륨마운트 경로**의 ..은 apache가 설치된 절대 경로 작성

- 사용자 정의 변수

| 사용자 정의 변수명  | 사용자 정의 변수 KEY   | 사용자 정의 변수 마운트 경로             | 접근권한(Optional) |
| -------------- | ------------------ | --------------------------------- | ---------------- |
| dev-ebiz-httpd | httpd-vhosts.conf  | .. /conf/extra/httpd-vhosts.conf  | 644              |
| dev-ebiz-httpd | httpd.conf         | .. /conf/httpd.conf               | 644              |

>> ※**사용자 정의 변수 마운트 경로**의 ..은 apache가 설치된 절대 경로 작성

- Build Script

>> **Dockerfile**
```bash
FROM 127.0.0.1:30001/httpd:2.4.38

#Jenkinsfile의 stageDockerBuild전에 resources 디렉토리를 컨테이너 내부에 생성
ADD resources /usr/local/apache2/htdocs/ebiz/resources

#그 후 docker를 build하여 image 생성 후 push
```

>> **정적 리소스** 의 경우 Jenkinsfile에서 SVN으로부터 resources 디렉토리 파일만 가져와서 resources 생성(**Jenkinsfile** 참조)
>> resources 디렉토리는 Dockerfile의 현위치와 동일하기 때문에, 위 ADD 명령을 통해서 container의 DocumentRoot 경로로 이동 시켜야 함
>> 사용자정의 변수 메뉴에서 httpd-vhosts.conf에서 **DocumentRoot** 설정

>> **Jenkinsfile**

- 인프라보안사무국에서 메타 정보 및 기본 함수 호출 작성하고 나서 작성
```bash
@Library('accordion-lib') _
accordionTemplate(logger: [level: "INFO"]) {
	//저장소(git, svn)에서 소스코드 또는 사용자가 업로드한 파일을 가져오는 단계
	stageGetSource()
	
	//resources checkout 받아서 디렉토리 생성
	stage('make resources') {
        sh "ls -al"
        sh "rm -rf .svn"
        sh "mkdir resources"
        sh "chmod -R 755 resources"
        sh "mv css resources/"
        sh "mv fonts resources/"
        sh "mv images resources/"
        sh "mv js resources/"
        sh "mv plugin resources/"
    }
	
	//소스코드를 빌드(maven, ant)하는 단계
	//우선순위 : mvn-accordion.xml -> mvn.xml -> build-accordion.xml -> build.xml -> 단계 SKIP
	// stageSourceBuild()
	
    // 도커 이미지을 만드는 단계(docker build)
    //stage 명칭 바뀌면 안 됩니다.
    stageDockerBuild()
	
	//도커 이미지를 레지스트리에 넣는 단계(docker push)
	stageDockerPush()
	
	//도커 이미지를 아코디언에 반영하는 단계
	stageDeploy()
}
```




> **ebiz-dev-was**

- Service Type: NodePort

- Ports

|    Name   | Container Port | Protocol | Node Port | Service Port |
| --------- | -------------- | -------- | --------- | ------------ |
| http-port | 8080           | TCP      | xxxxx     | 8080         |
| ajp-port  | 8009           | TCP      | xxxxx     | 8009         |

- Environment Variables (**기존 key값에 아래 key:value만 추가**)

| Key          | Value               |
| ------------ | ------------------- |
| INSTANCE_ID  | -Dinstance_id=dev   |
| JAVA_OPTS    | $(INSTANCE_ID)      |

>> ※운영은 **instance_id=prod** (logback.xml 참조)

- 사용자 정의 변수

| 사용자 정의 변수명        | 사용자 정의 변수 KEY   | 사용자 정의 변수 마운트 경로  | 접근권한(Optional) |
| -------------------- | ------------------ | --------------------- | ---------------- |
| dev-ebiz-tomcat-conf | context.xml        | .. /conf/context.xml  | 644              |
| dev-ebiz-tomcat-conf | server.xml         | .. /conf/server.xml   | 644              |

>> ※**사용자 정의 변수 마운트 경로**의 ..은 tomcat이 설치된 절대 경로 작성

- Build Scrpt

>> **Dockerfile**

- 인프라보안사무국에서 작성

>> **Jenkinsfile**

```python
// stageSourceBuild() 기본 maven 빌드 주석처리
stage('maven build', 'maven'){
	sh 'mvn clean package -P dev'
}
```
- pom.xml의 profile 적용을 위하여 -P 옵션 적용, 운영은 prod로 적용

- host에서 container로 배포 시, 아키텍쳐(메뉴얼 확인) 구조를 파악 필요

- SAP 연동을 위한 파일 copy 부분 및 권한 설정과 SVN으로부터 Source Checkout 부분 확인

- stageDockerBuild는 인프라보안사무국에서 stageEbizDockerBuild로 커스터마이징 필요



3. 빌드

- SVN / Git 저장소에 커밋된 최종 소스를 빌드하여 서버에 배포

- ebiz-dev-was 클릭하여, 우측 상단 빌드 클릭 (제목/내용은 필요시 작성)

- Dockerfile과 Jenkinfile은 앱 설정 시 작성한 내용을 그대로 따르며, 빌드에서 수정한 내용은 실제 앱 내용에 반영됨 (1회성 아님)

- **정적리소스** 배포는 Web 서버 빌드, 그 외에는 Was 빌드


4. 네트워크

- 라우터 생성 (**dev-ebiz-web만 설정**)

> 호스트명 : devebiz.oilbank.co.kr

> 노드포트 : 인프라보안사무국에서 open 해준 port 번호

> 서비스포트 : 80

> 타겟포트 : 80 (TCP)

> externalIPs는 인프라보안사무국에 요청(외부접근 domain ip)



5. 볼륨

- Volumn은 ebook, was log, web log, upload 디렉토리 총 4개를 설정

- ebook은 업무매뉴얼 정적 리소스, web/was log와 upload(파일 업로드) 디렉토리 3개 볼륨 설정해서 NAS와 연동 요청



6. 사용자 정의 변수

> apache - tomcat 연동을 위한 설정 파일 추가 및 설정

> container 생성 시, 해당 설정 파일을 생성 또는 덮어쓰기를 해줌

> 2번 앱에서 사용자 정의 변수에서 추가해주면 적용 완료
