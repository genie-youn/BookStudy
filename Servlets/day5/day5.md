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
컨테이너가 다 한다. 속성 클래스를 만들어 인스턴스화 하고, DD에 파라미터를 입력한다. 리스너는 이벤트를 감지하여 속성을 설정하고 서블릿에서 그 속성을 가져와 사용한다.

###속성이란 정확히 무엇인가?
속성은 3개의 API 객체, 즉 ServletContext, HttpServletRequest, HttpSession 객체 중 하나에 설정해놓는 객체를 말한다.
내부적으로 이것이 어떻게 구현되어 있는지 알 수 없고, 신경 쓸 필요도 없다. 우리는 누가 이 값을 볼 수 있으며, 얼마나 오랫동안 이 객체가 살아있는지만 신경쓰면 된다.(Scope)

###속성은 파라미터가 아니다.
**속성**
타입 : Appication/context, Request, Session
설정 메소드 : setAtrribute(String name, Object value)
리턴 타입 : Object
참조 메소드 : getAtrribute(String name)

**파라미터**
타입 : Appication/context 초기화 파라미터, Request 파라미터, Servlet 초기화 파라미터(Session 파라미터라는건 없다.)
설정 메소드 : 오직 런타임에서만 DD에서 가능
리턴 타입 : String
참조 메소드 : getInitParamter(String name)

이 중 리턴 타입이 가장 큰 차이점이다.

###세 가지 속성의 생존범위(Scope)
Context : 애플리케이션 안의 모든 것
Session : 특정 HttpSession에 접근 권한을 가진 것만
Request : 특정 ServletRequest에 접근 권한을 가진 것만

###Context 생존범위는 스레드 안전하지 못하다.
애프리케이션에 있는 누군가가 컨텍스트 속성에 접근할 수 있다. (다른 서블릿이든, 같은 서블릿의 다른 스레드든)
이는 임계영역(Critical section)의 상호배제(Mutual Exclusive) 문제와 동일하다.

###어떻게 하면 해결할 수 있나?
서비스 메소드를 동기화 방법은 한 서블릿 인스턴스에서의 스레드 접근은 막을 수 있지만, 다른 스레드에서의 접근은 막을 수 없다. -> 컨텍스트에 락을 걸면 된다.
```
synchronized(getServletContext()) {
	getServletContext().setAtrribute("foo", "22");
	getServletContext().setAtrribute("bar", "44");

	...
}
```

###세션 속성은 스레드 안전인가?
하나의 클라이언트(웹 브라우저 1개)에서는 스레드 안전이라고 말할 수 있겠으나 클라이언트가 여러 개고 동시에 요청을 보낸다면 동기화가 필요하다. 이는 HttpSession에서 진행된다.
```
HttpSession session = request.getSession();

synchronized(session) {
	getServletContext().setAtrribute("foo", "22");
	getServletContext().setAtrribute("bar", "44");

	...
}
```

**동기화를 사용할 때는 신중을 기해야한다. 무언가를 보호할 때 동기화를 사용하되 '가능한한 짧은 시간동안' 걸어야한다.**

###Single Tread Model(STM)은 인스턴스 변수를 보호하려고 설계되었다.
SingleThreadModel 인터페이스를 구현하면 동기화를 보장해준다. 그 방법은 크게 두가지이다.

1. 요청을 큐로 처리하는 방법 : 하나의 컨테이너가 요청 큐에 요청을 쌓고 하나씩 꺼내서 서블릿에 뿌린다.
2. 요청을 풀링하는 방법 : 각 요청을 한 서블릿의 서로 다른 인스턴스에 각각 뿌린다.

하지만 STM 방법도 클래스 변수에는 별 도움이 되지 못한다. -> 요즘에 안쓴다. (서블릿 API에 STM이 아예 없음)

###오직 Request 속성과 지역 변수만이 스레드 안전이다.
STM은 사용하면 안된다. 그러면 어떻게 해야하는가?

1. 변수를 인스턴스 변수가 아닌 서비스 메소드의 지역변수로 선언한다.
2. Context, Session, Request 중에서 가장 적당한 속성의 생존범위를 사용한다.

###Response 객체를 넘겨주고 나면(flush) 그 후에 Request를 넘기려고 해도 소용없다. 이것은 IllegalStateException을 띄운다.
