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
