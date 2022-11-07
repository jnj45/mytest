# Spring Mobile 적용

- 참고페이지 : http://arahansa.github.io/docs_spring/device.html

## 환경설정

### pom.xml

```xml
<dependency>
    <groupId>org.springframework.mobile</groupId>
    <artifactId>spring-mobile-device</artifactId>
    <version>1.1.5.RELEASE</version>
</dependency>
```

### Spring 설정

#### egov-com-servlet.xml

```xml
<mvc:interceptors>
    <!-- spring mobile switcher 설정 -->
    <mvc:interceptor>
        <mvc:mapping path="/**" />
        <bean class="org.springframework.mobile.device.DeviceResolverHandlerInterceptor"/>
    </mvc:interceptor>
    <mvc:interceptor>
        <mvc:mapping path="/**" />
        <bean class="org.springframework.mobile.device.site.SitePreferenceHandlerInterceptor" />
    </mvc:interceptor>
    <mvc:interceptor>
        <mvc:mapping path="/**" />
        <bean class="org.springframework.mobile.device.switcher.SiteSwitcherHandlerInterceptor" factory-method="mDot">
            <constructor-arg index="0" type="java.lang.String" value="hipp.oilbank.co.kr"/>
            <constructor-arg index="1" type="java.lang.Boolean" value="true"/>
        </bean>
    </mvc:interceptor>  
    <!-- spring mobile switcher 설정 -->
    
    ......
<!-- Tiles -->
<bean id="tilesConfigurer" class="org.springframework.web.servlet.view.tiles3.TilesConfigurer">
    <property name="definitions">
        <list>
            <value>/WEB-INF/tiles/tiles_conf.xml</value>
            <value>/WEB-INF/tiles/tiles_mobile_conf.xml</value><!-- 모바일용 TILES설정파일 -->
        </list>
    </property>
</bean>

<!-- spring mobile switcher 적용 시 -->
<bean class="org.springframework.mobile.device.view.LiteDeviceDelegatingViewResolver" p:order="1">
    <constructor-arg>
        <bean class="org.springframework.web.servlet.view.UrlBasedViewResolver" 
              p:viewClass="org.springframework.web.servlet.view.tiles3.TilesView"
              />
    </constructor-arg>
    <property name="mobilePrefix" value="mobile/" />
    <property name="tabletPrefix" value="mobile/" />
    <property name="enableFallback" value="true" />
</bean>    
```

### Tomcat 설정

#### context.xml

```xml
<Context sessionCookieDomain=".hipp.oilbank.co.kr"><!-- spring mobile mDot SiteSwitcher 적용으로 서브도메인 세션공유 시 필요. 이걸 적용하면 localhost 로그인 안 됨. 윈도우 hosts 파일에 로컬ip 도메인 등록 후 access -->
    ......
    <!-- spring mobile mDot SiteSwitcher 적용 시 필요. -->
	<CookieProcessor className="org.apache.tomcat.util.http.LegacyCookieProcessor"/>
</Context>    
```

### Tiles 설정

#### tiles_mobile_conf.xml : 모바일 페이지용 tiles설정 파일 (예시)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE tiles-definitions PUBLIC
       "-//Apache Software Foundation//DTD Tiles Configuration 3.0//EN"
       "http://tiles.apache.org/dtds/tiles-config_3_0.dtd">

<tiles-definitions>

	<!--   신규 퍼블리싱 (메인) -->
	<definition name="mobile_layoutHigisMain" template="/WEB-INF/tiles/layout/mobile/layoutHigisMain.jsp" preparer="net.ecbank.fwk.tiles.MenuViewPreparer">
		<put-attribute name="head" value="/WEB-INF/tiles/layout/higisHeader.jsp" />
	</definition>

	<!--   신규 퍼블리싱 (메뉴 있는) -->
	<definition name="mobile_layoutHigisDefault" template="/WEB-INF/tiles/layout/mobile/layoutHigisDefault.jsp" preparer="net.ecbank.fwk.tiles.MenuViewPreparer">
		<put-attribute name="head" value="/WEB-INF/tiles/layout/higisHeader.jsp" />
		<put-attribute name="foot" value="/WEB-INF/tiles/layout/higisFooter.jsp" />
	</definition>

	<!--   신규 퍼블리싱 (WindowPopup) -->
	<definition name="mobile_layoutHigisDefault" template="/WEB-INF/tiles/layout/mobile/layoutHigisDefault.jsp" preparer="net.ecbank.fwk.tiles.MenuViewPreparer">
		<put-attribute name="head" value="/WEB-INF/tiles/layout/higisHeader.jsp" />
		<put-attribute name="foot" value="/WEB-INF/tiles/layout/higisFooter.jsp" />
	</definition>

	<!--   tiles 레이아웃 (메뉴 없는, copylight 포함) -->
	<definition name="mobile_layoutHigisNoGnb" template="/WEB-INF/tiles/layout/mobile/layoutHigisNoGnb.jsp" preparer="net.ecbank.fwk.tiles.MenuViewPreparer">
		<put-attribute name="head" value="/WEB-INF/tiles/layout/higisLoginHeader.jsp" />
	</definition>

	<!--   tiles 기본 레이아웃 (조회 팝업 - WindowPop) -->
 	<definition name="mobile_layoutHigisSearchPopup" template="/WEB-INF/tiles/layout/mobile/layoutHigisSearchPopup.jsp" preparer="net.ecbank.fwk.tiles.MenuViewPreparer">
		<put-attribute name="head" value="/WEB-INF/tiles/layout/higisHeader.jsp" />
		<put-attribute name="foot" value="/WEB-INF/tiles/layout/higisFooter.jsp" />
	</definition>

	<!-- 오즈 레포트 뷰어 팝업 레이아웃 -->
	<definition name="mobile_layoutOzPopup" template="/WEB-INF/tiles/layout/mobile/layoutHigisNoGnb.jsp" preparer="net.ecbank.fwk.tiles.MenuViewPreparer">
		<put-attribute name="head" value="/WEB-INF/tiles/layout/ozHeader.jsp" />
	</definition>

	<!-- ##### 신규 퍼블리싱 적용 영역 시작 ############################################################################### -->
	<!-- 로그인 -->
	<definition name="mobile/egovframework/com/uat/uia/EgovLoginUsr" extends="mobile_layoutHigisNoGnb">
		<put-attribute name="content" value="/WEB-INF/jsp/mobile/egovframework/com/uat/uia/EgovLoginUsr.jsp" />
	</definition>

	<!-- 업무 공통 팝업 -->
	<definition name="mobile/cmn/*Pop" extends="mobile_layoutHigisSearchPopup">
		<put-attribute name="content" value="/WEB-INF/jsp/mobile/cmn/{1}Pop.jsp" />
	</definition>
	<definition name="mobile/biz/cmn/*Pop" extends="mobile_layoutHigisSearchPopup">
		<put-attribute name="content" value="/WEB-INF/jsp/mobile/biz/cmn/{1}Pop.jsp" />
	</definition>

	<!-- 메인 -->
	<definition name="mobile/main/*" extends="mobile_layoutHigisMain">
		<put-attribute name="content" value="/WEB-INF/jsp/mobile/main/{1}.jsp" />
	</definition>

	<!-- admin -->
	<definition name="mobile/admin/*/*Pop" extends="mobile_layoutHigisDefault">
		<put-attribute name="content" value="/WEB-INF/jsp/mobile/admin/{1}/{2}Pop.jsp" />
	</definition>
	<definition name="mobile/admin/*/*" extends="mobile_layoutHigisDefault">
		<put-attribute name="content" value="/WEB-INF/jsp/mobile/admin/{1}/{2}.jsp" />
	</definition>

	<!-- 게시판 -->
	<definition name="mobile/biz/bbs/*Pop" extends="mobile_layoutHigisDefault">
		<put-attribute name="content" value="/WEB-INF/jsp/mobile/biz/bbs/{1}Pop.jsp" />
	</definition>
	<definition name="mobile/biz/bbs/*" extends="mobile_layoutHigisDefault">
		<put-attribute name="content" value="/WEB-INF/jsp/mobile/biz/bbs/{1}.jsp" />
	</definition>
	
	<!-- higis 업무 -->
	<definition name="mobile/higis/*/*Pop" extends="layoutHigisDetailPopup">
		<put-attribute name="content" value="/WEB-INF/jsp/higis/{1}/{2}Pop.jsp" />
	</definition>
	<definition name="mobile/higis/*/*" extends="layoutHigisDefault">
		<put-attribute name="content" value="/WEB-INF/jsp/higis/{1}/{2}.jsp" />
	</definition>
	
</tiles-definitions>
```

주의사항

- Controller에서 return 페이지의 경로와 tiles의 경로가 일치하여야 한다. 따라서, tiles definition nam처럼 경로 맨 앞에 '/'가 없도록 controller return 경로를 설정해야 함.

## 모바일용 JSP 파일 경로

- /src/main/webapp/WEB-INF/jsp/mobile 디렉토리를 기준으로 normal페이지와 대응하는 동일한 경로로 jsp파일을 생성한다.

## RealGrid 라이선스

- mDot Switcher를 적용하여 접근할 때는 domain으로 access해야 하므로 (localhost 접속 시 로그인 안 됨.), realgrid 라이선스도 로컬과 개발에서 사용할 도메인으로 라이선스의 도메인을 설정하여 받는다.

