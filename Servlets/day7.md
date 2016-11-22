#JSP를 사용해보자
##JSP는 서블릿이 된다.
개발자가 서블릿을 만들지 않지만, 컨테이너는 알아서 JSP를 서블릿 소스 코드로 변환한 후 컴파일하여 완전한 서블릿 클래스로 만든다.

###JSP의 변천사
MyJSP.jsp -> MyJSP_jsp.java -> MyJSP_jsp.class -> MyJSP_jsp Servlet

###page 지시자
JSP가 java 패키지를 import 하기 위해서는 page 지시자가 필요하다. 지시자의 형식은 다음과 같다.  
`<%@ page import="foo.*" %>`

패키지를 여러개 import 하는 경우는 다음과 같다.  
`<%@ page import="foo.*, java.util.*" %>`

###표현식
표현식은 파라미터를 주어 바로 출력시키기 위해 사용한다. 이는 out.println() 따위를 안써도 되게 해준다.

스크립틀릿 코드 : `<% out.println(Counter.getCount()); %>`  
표현식 코드 : `<%= Counter.getCount() %>`

표현식 안에 들어가는 내용은 out.println() 안의 인자가 되므로 ;(세미콜론)을 쓰면 안된다.
표현식에서는 리턴값이 void인 메소드는 사용할 수 없다.

###선언문
일반적으로 스크립틀릿 안에서 선언되는 변수는 지역변수이다. 이와는 다르게 전역 변수나 인스턴스 변수를 선언해주고 싶을 때 선언문을 사용한다.  
`<%! int count=0 %>`

이렇게 하면 서블릿으로 변경되었을 때 이렇게 된다.
```
public class basicCounter_jsp extends SomeSpecialHttpServlet {
	int count =0;
	public void _jspService(HttpServletRequest request, HttpServletResponse response) throws java.io.IOException {
		...
	}
}
```

다음과 같이 메소드를 정의할 수도 있다.
```
<%! int doubleCount() {
	count = count*2;
	return count;
	}
%>
```

###주석은 이렇게
* HTML 주석  
`<!-- HTML comment -->`

* JSP 주석  
`<%-- JSP comment --%>`
