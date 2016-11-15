#웹 애플리케이션이 되어 보자
##초기화 파라미터
###DD 파일에서
```
<init-param>
	<param-name>adminEmail</param-name>
	<param-value>lhbv1421@hanmail.net</param-value>
</init-param>
```

###서블릿 코드에서
`out.println(getServletConfig().getInitParamete("adminEmail"));`

##서블릿 초기화가 된 다음에 서블릿 초기화 파라미터를 사용할 수 있다.
컨테이너가 서블릿을 초기화 할때, 서블릿마다 하나씩 ServletConfig를 생성한다.
컨테이너는 DD에서 서블릿 초기화 파라미터를 읽어, 이 정보를 ServleConfig로 넘겨준다.
그 다음 ServlectConfig를 서블릿의 init() 메소드에 제공한다.

컨테이너가 서블릿을 초기화 할때 **단 한번만** 서블릿 초기화 파라미터를 읽는다.

##JSP에서 서블릿 초기화 파라미터에 접근하는 방법
-> Request의 속성을 이용하여 JSP로 모델 정보를 넘긴다.
```
// do Post() 메소드 안
String color = request.getParameter("color"); //Request 객체에서 사용자가 선택한 정보를 읽어오는 부분

BeerExpert be = new BeerExpert(); //모델 인스턴스화, 뷰에 필요한 정보 설정
List result = be.getBrands(color);

request.setAttribute("Styles", result); //Request 객체의 속성에 결과 설정, JSP에 포워딩
```

##모든 웹 애플리케이션이 정보를 공유하려면?
서블릿 초기화 파라미터는 서블릿에만 적용 되므로 모든 웹 애플리케이션이 이용할 수는 없다.
따라서 컨텍스트 초기화 파라미터를 사용하여 애플리케이션 전역 설정을 해야한다.

###DD 파일에서
```
//<servlet> 항목에서 <init-param>을 모두 제거한다.
//<servlet> ... </servlet>
<context-param> 
	<param-name>adminEmail</param-name>
	<param-value>lhbv1421@hanmail.net</param-value>
</context-param>
//<context-param> 은 웹 애플리케이션 전역 설정이므로 <servlet> 안에 들어가지 않는다.
```

###서블릿 코드에서
`out.println(getServletContext().getInitParameter("adminEmail"));`
 
##ServletConfig는 서블릿 당 하나, ServletContext는 웹 애플리케이션 당 하나가 할당된다.
따라서 ServletContext가 상위 개념이라고 할 수 있다.

##ServletContext로 할 수 있는 일
ServletContext는 JSP나 서블릿을 컨테이너 또는 다른 웹 애플리케이션과 연결하는 끈이다.
ServletContext의 메소드에는 getAttribute(String), setAttribute(String, Object), removeAttribue(String)등이 있다. 이들은 초기화 파라미터에 대한 Getter와 속성에 대한 Getter/Setter로 이루어져 있다.

##초기화 파라미터로 데이터베이스 DataSource 객체를 저장 할수는 없을까?
컨텍스트 파라미터에는 String 밖에 저장할 수 없다. 객체를 DD에 넣으려면 직렬화 시켜서 집어넣는 방법 밖에 없는데 그런 서블릿 스펙은 없다.
애플리케이션에 있는 모든 서블릿이 데이터베이스 DataSource 객체를 공유하고자 할때는 컨텍스트 파라미터에 DataSource 검색명(Lookup Name)을 저장하고, 이 검색명 String을 실제 DataSource 객체로 바꾸는 방법이 있다. 
**그렇다면 누가 이 작업을 할것인가? 서블릿에다가 하기에는 어떤 서블릿이 먼저 호출될지 알 수 없다.**

##그것이 바로 리스너이다.
우리가 원하는 것은 컨텍스트 초기화 이벤트에 리스닝을 하여 초기화 파라미터를 읽은 다음 애플리케이션이 클라이언트에게 서비스하기 전에 특정 코드를 실행할 수 있도록 하는 것이다.
애플리케이션 초기화를 위한 서블릿이나 JSP가 아닌 **다른 자바 객체**가 필요하다.

###그 이름은 ServletContextListner이다.
다음과 같은 기능을 가진 클래스가 필요하다.
* 컨텍스트가 초기화되는 걸 알아차릴 수 있어야 한다.
	* ServletContext로부터 컨텍스트 초기화 파라미터를 읽는다.
	* 데이터베이스를 연결하기 위하여 초기화 파라미터 검색명을 사용한다.
	* 데이터베이스 Connection 객체를 속성에 저장한다.

* 컨텍스트가 종료되는 걸 알아차릴 수 있어야 한다.
	* 데이터베이스 연결을 닫는다.

**ServlectContextLister 클래스 :**
```
import javax.servlet.*;

public class MyServletContextListener implements ServletContextListener (
	public void contextInitialized (ServletContextEvent event) {
		//데이터베이스 연결 초기화
		//컨텍스트 속성에 저장
	}

	public void contextDestroyed (ServletContextEvent event) {
		//데이터베이스 연결을 닫음
	}
)
```

###리스너 클래스를 만들었다. 그렇다면 이 클래스를 어디에, 누가 배포하고 인스턴스화 하는가? 이벤트 등록과 속성 설정 방법은?
