# 스프링 시큐리티 관련 설정

## context-security.xml 수정

* 권한 계층형 구조는 사용하지 않고, 복수권한 방식으로 사용하기 위한 작업   
  context-security.xml에 sqlHierarchicalRoles 부분을 아래와 같이 수정   
  모든 권한은 ROLE_ANONYMOUS 를 상속받고, ROLE_ADMIN는 모든 권한을 상속받는 구조로 조회되도록 쿼리 수정
      
```xml
sqlHierarchicalRoles ="SELECT 'ROLE_ANONYMOUS' as parent, AUTHOR_CODE AS child
	  				     FROM EF_AUTH
						WHERE AUTHOR_CODE LIKE 'ROLE_%'
						  AND AUTHOR_CODE != 'ROLE_ANONYMOUS' "
```

* 위 계층구조와 리소스접근제한 role을 제대로 적용할려면, ROLE_ADMIN에 부여하는 모든URL 접근가능 ROLE(\A/.*\.do.*\Z)의 우선순위가 맨 마지막이 되도록, ROLE_SORT 값을 최대한 크게 잡는다.
  
* 사용자별 권한 테이블(COMTNEMPLYRSCRTYESTBS -> EF_USER_AUTH)에 사용자별로 복수권한을 등록,수정할 수 있도록 관리콘솔 화면개발

## 전자정부 security runtime 소스 직접 import
* 시큐리티 처리관련 커스트마이징을 위해 소스를 직접 프레임워크 소스에 포함시키고 일부 수정함.
* resources/META-INF/ 에 전자정부 시큐리티 간소화 설정파일들이 있음.

## eGov spring security 관련 주요 클래스

* EgovSecuritySecuredObjectConfigBeanDefinitionParser   
  : egov-security schema namespace 'secured-object-config' element 처리를 담당하는 bean definition parser 클래스
  
* EgovReloadableFilterInvocationSecurityMetadataSource   
  : 보호자원 접근관리 DB Metasource 처리
  : getAttributes(Object object) 메소드는 액세스여부처리가 role-auth 데이타 순서에 의존적인 부분을 해결하기 위해 role-auth 데이타 전체를 스캔해서 해당되는 auth 리스트 전부를 리턴할 수 있도록 수정.
  
* EgovSecuredObjectServiceImpl      
  : Spring Security의 초기 데이터를 DB로 부터 조회하여 보호된 자원 접근 권한을 지원, 제어 할 수 있도록 구현한 클래스
  
* SecuredObjectDAO   
  : DB 기반의 Secured Object 정보를 제공하기 위한 DAO

## 로그인/로그아웃 처리 관련 클래스
* 로그인 컨트롤러 : egovframework.com.uat.uia.web.EgovLoginController
* 로그인 필터 : egovframework.com.sec.security.filter.EgovSpringSecurityLoginFilter
* 로그인 시 사용자정보와 로그인세션정보 맵핑처리 : egovframework.com.sec.security.common.EgovSessionMapping
* 로그아웃 필터 : egovframework.com.sec.security.filter.EgovSpringSecurityLogoutFilter



## 커스트마이징

- net.ecbank.fwk.security 패키지에 추가
- JdbcUserDetailsManager : 부서 권한 적용하면서 jdbcAuthoritiesByUsernameQuery가 변경되고 쿼리바인딩변수가 2개가 필요해서 커스트마이징함. 
- SecurityManageController : 접근권한 오류발생 시 에러페이지 이동 처리하기 위해 추가.



