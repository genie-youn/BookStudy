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

###변환과 컴파일은 딱 한번만 하면 된다.
JSP 파일을 배포하는 경우, 서블릿으로 변환하고 컴파일 하는 작업은 JSP에게 단 한번만 이루어진다. 일단 서블릿이 되고나면 다른 서블릿과 완전히 똑같이 행동한다. 따라서 첫번째 요청은 시간이 걸린다고 할 수 있는데, 이는 "!jsp_precompile" 쿼리를 통해 미리 변환과 컴파일을 함으로써 해결할 수 있다.

##JSP 초기화하기
JSP도 일반 서블릿과 마찬가지로 초기화 작업을 할 수 있다. 하지만 그 방식은 일반 서블릿과 조금 다르다.

###서블릿 초기화 파라미터 설정
```
<web-app>
...
	<servlet>
		<jsp-file>/TestInit.jsp</jsp-file>
		...
	</servlet>
	...
</web-app>
```

###jspInit() 정의
```
<%!
	public void jspInit() {
		ServletConfig sConfig = getServletConfig();
		...
	}
%>
```

##PageContext로 속성 접근하기
###PageContext를 이용하여 속성을 읽고, 설정하는 예제
* page 생존범위의 속성 세팅  
`<% pageContext.setAttribute("foo",one); %>`

* page 생존범위의 속성 읽기  
`<%= pageContext.getAttribute("foo") %>`

* pageContext를 이용하여 session 생존범위 속성 세팅하기  
`<% pageContext.setAttribute("foo",two,PageContext.SESSION_SCOPE); %>`

* pageContext를 이용하여 session 생존범위 속성 읽기  
`<%= pageContext.getAttribute("foo",PageContext.SESSION_SCOPE) %>`

* pageContext를 이용하여 application 생존범위 속성 읽기  
`<%= pageContext.getAttribute("mail", PageContext.APPLICATION_SCOPE) %>`

* pageContext를 이용하여 어떤 생존범위인지 모르는 속성 찾기  
`<%= pageConext.findAttribute("foo") %> //속성은 가장 작은 범위에서부터 넓은 범위의 순으로 찾는다.`

##Expression Language
###스크립틀릿을 쓰면 유지보수가 번거롭다.
웹 디자이너 같은 비프로그래머들은 자바가 뭔지 모른다.  
"당신 옆집에 사는 정신병자가 당신이 작성한 코드를 유지보수할 사람이다."라는 생각으로 코딩하라.

###EL의 표현식  
`${applicationScope.mail} //<%= application.getAttribute("mail") %>와 같은 의미`

###스크립틀릿을 막을 수 있다.
`<scripting_invalid>` 태그를 DD에 포함시키면 스크립틀릿, 표현식, 선언문을 JSP에서 사용할 수 없게 된다.  
`<%@ page isScriptingEnabled="false" %>` 같은 스펙도 초안에는 있었으나 최종적으로 삭제되었다.

###EL도 못쓰게 막을 수 있다.
* DD에 <el-ignored> 태그 추가
```
<jsp-config>
	<el-ignored>
		true
	</el-ignored>
</jsp-config>
```

*page 지시자 속성 isELIgnored 추가  
`<%@ page isELIgnored="true" %>`
