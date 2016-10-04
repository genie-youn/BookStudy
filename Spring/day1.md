
#오브젝트와 의존관계#
###관심사의 분리 
* 미래의 변화를 대비하기 위해 관심이 같은것 끼리는 하나의 객체안으로 관심이 다른것끼리는 서로에 영향을 주지 않도록 분리한다.

###메소드추출기법
* 공통된 기능을 담당하는 메소드로 중복된 코드를 뽑아내는 것.

###템플릿메소드패턴
* 슈퍼클래스에 기본적인 로직의 흐름을 만들고, 그 기능의 일부를 추상메소드나 오버라이딩 가능한 Protected 메소드로 만든 후 서브클래스에서 필요에 맞게 구현하도록 사용하는 방법

###팩토리메소드패턴
* 서브클래스에서 구체적인 오브젝트 생성 방법을 결정하게 함.

##인터페이스의 도입
###추상화
* 공통된 특성을 뽑아내 이를 따로 분리. 어떤일을 하겠다는 기능만 정의
* 클래스를 분리했을때 갖는 한계는 종속관계에 놓인다는것이다. 이 긴밀한 연결을 없애기 위해 추상적인 연결고리를 만드는데 이것이 바로 인터페이스
* 모든곳에서 인터페이스를 사용함으로써 구체적인 정보는 모두제거할 수 있다.

###관계설정책임의 분리
* 인터페이스를 사용함으로써 온전히 분리해내나 싶었지만, 생성자코드에선 구체적인 클래스를 명시할 수 밖에 없다.
* 클래스 사이에 관계를 만드는 것이 아니라, 오브젝트 사이에 다이내믹한 관계로 만들자.
* 클라이언트에게 책임을 떠넘긴다.

##원칙과 패턴
###개방폐쇄원칙
* 클래스나 모듈은 확장에는 개방(낮은 결합도) 변경에는 닫혀야(높은 응집도) 한다.
* 높은 응집도 : 변화가 일어나면 모듈의 변경 부분이 많다. 즉 관심사가 일치한다.
* 낮은 결합도 : 하나의 오브젝트가 변경될 때 관계를 맺고있는 다른 오브젝트에 주는 영향이 적다.

###전략패턴
* 자신의 context에서 필요에 따라 변경이 필요한 알고리즘을 전부 인터페이스를 통해 외부로 분리해버리고, 이를 구현한 구체적인 클래스(전략)을 필요에 따라 바꿔서 사용
* 이때 클라이언트는 context에게 자신이 사용할 전략을 전달한다.

##제어의역전(IoC)
###오브젝트 팩토리
* 팩토리 : 오브젝트를 생성하는 쪽과 생성된 오브젝트를 사용하는 쪽의 역할과 책임을 깔끔하게 분리하기 위해 객체의 생성방법을 결정하고 생성된 객체를 돌려주는 클래스
* 설계도로서의 팩토리 : 애플리케이션의 컴포넌트(로직)을 담당하는 오브젝트와 구조를 결정하는 오브젝트의 분리

###제어의 역전
* 프로그램의 제어 흐름 구조가 뒤바뀌는것
###일반적인 프로그램의 흐름
* 프로그램이 시작되는 지점에서 다음에 사용할 오브젝트를 결정하고, 오브젝트를 생성하고, 만들어진 오브젝트 메소드를 호출..
* 즉 각 오브젝트는 프로그램의 흐름이나 사용할 오브젝틀르 구현하는데 능동적으로 참여
###다시, 제어의 역전이란
* 오브젝트가 자신이 사용할 오브젝트를 스스로 선택하지 않는다. 자신도 어디서 어떻게 생성되는지 모른다.
* 모든 제어권한을 위임

##스프링의 IoC
###스프링 빈
* 스프링이 제어권을 가지고 직접 만들고 관계를 부여하는 오브젝트

###빈 팩토리
* 빈의 생성과 관계설정 같은 제어를 담당하는 IoC 오브젝트

###@Configuration
* 스프링이 빈 팩토리를 위한 오브젝트 설정임을 인식하게 하는 어노테이션

###@bean
* 오브젝트를 만드는 메소드임을 알리는 어노테이션

###AnnotationConfigApplicationContext
* ApplicationContext를 상속받은 클래스로 @Configuration 어노테이션이 붙은 자바코드를 관계설정정보로 사용한다

##싱글톤 레지스트리와 오브젝트 스코프
###오브젝트의 동일성과 동등성
* 동일성 : 하나의 오브젝트만 존재, 두개의 오브젝트 레퍼런스 변수를 가질뿐, ==연산자로 비교
* 동등성 : 두개의 각기다른 오브젝트가 메모리에 적재, 동일한 정보를 갖는다. equals 연산자로 비교
* 스프링은 여러번에 걸쳐 요청하더라도 매번 동일한 오브젝트를 돌려준다

###싱글톤 레지스트리로서의 애플리케이션 컨텍스트
* 애플리케이션 컨텍스트는 IoC컨테이너이자 싱글톤을 관리하고 저장하는 싱글톤 레지스트리다.

###서버어플리케이션과 싱글톤
* 많은 양의 트래픽을 처리해야 하는 엔터프라이즈 서버환경을 위해 설계된 스플링은 요청시마다 객체를 생성하는건 오버헤드가 너무크다.
* 그래서 싱글톤을 선택

###자바의 싱글톤 패턴 구현 한계
* private 생성자를 갖고 있기 때문에 상속할 수 없다.
* 테스트에 어려움이 있다.
* 서버환경에서는 싱글톤이 하나만 만들어지는것을 보장하지 못한다.
* 싱글톤의 사용은 전역상태를 만들 수 있기 때문에 바람직하지 못하다.

###싱글톤 레지스트리
* 자바의 기본적인 싱글톤 패턴의 구현방식의 한계를 극복하기 위해 직접 싱글톤 형태의 오브젝트를 만들고 관리하는 기능을 제공
* 평범한 자바클래스를 싱글톤으로 활용하게 해준다! 어떻게? IoC를 통해!

###싱글톤과 오브젝트의 상태
* Stateless : 상태정보를 내부에 갖고 있지 않음
* Stateful : 인스턴스의 필드값을 변경하고 유지한다.

##의존관계 주입 (DI)
###제어의 역전과 의존관계 주입
* 스프링이 제공하는 IoC의 핵심은 의존관계 주입

###런타임 의존관계 설정
* 의존관계에는 방향성이 있다.
* 의존한다는건 의존대상 B가 변하면 A에 영향을 끼친다는것
* A가 B에 의존한다 라고 표현한다.
* 의존관계 주입은 구체적인 의존 오브젝트와 그것을 사용할 주채, 보통 클라이언트라 부르는 오브젝트를 런타임에 연결해 주는 작업을 한다.
* 클래스 모델이나 코드에는 런타임 시점의 의존관계가 드러나지 않는다.
* 그러기 위해서는 인터페이스에만 의존하고 있어야 한다.
* 런타임 시점의 의존관계는 컨테이너나 팩토리같은 제3의 존재가 결정한다.
* 의존관계는 사용할 오브젝트에 대한 레퍼런스를 외부에서 제공해줌으로써 만들어진다.

###의존관계 주입
* DI컨테이너에 의해 런타임시에 의존 오브젝트를 사용할 수 있도록 그 레퍼런스를 전달받는 과정이 마치 메소드(생성자)를 통해 DI컨테이너가 주입해주는것 같아 의존성 주입이라 한다.

###의존관계검색
* 자신이 필요한 의존 오브젝트를 능동적으로 찾는다
* 런타임시 의존관계를 맺을 오브젝트를 결정하는것과 오브젝트를 생성하는 작업은 외부 컨테이너에게 맡기지만, 이를 가져올때는 메소드나 생성자을ㄹ 통한 주입대신 스스로 컨테이너에게 요청한다.

###DI가 갖는 장점
* 기능구현 교환
* 부가기능 추가

###메소드를 이용한 의존관계 주입
* 수정자메소드(Setter)를 이용한 주입
* 일반 메소드를 이용한 주입

##Xml을 이용한 설정
###Xml설정
* 스프링의 애플리케이션 컨텍스트는 xml에 담긴 DI정보를 활용할 수 있다. DI정보가 담긴 xml파일은 <beans>를 루트 엘리먼트로 사용한다.
* 빈의 이름: @bean 메소드 이름, getBean()메소드에 대상이 된다.
* 빈의 클래스 : 빈 오브젝트를 어떤 클래스를 이용하여 만들지
* 빈의 의존 오브젝트 : 빈의 생성자나 수정자 메소드를 통해 의존 오브젝트를 주입
* 자바빈의 관례를 따라 수정자메소드는 프로퍼티가 된다

###GenericXmlApplicationContext
* xml을 통해 의존관계정보를 이용하는 IoC/DI 컨테이너

###DataSrouce 인터페이스
* 자바에서 DB커넥션을 가져오는 오브젝트의 기능을 추상화한 인터페이스
* 다양한 방법으로 DB연결과 pooling을 구현한 클래스가 이미 많이 존재하므로 직접 구현해서 사용할 일은 거의없다.

##정리
* 먼저 책임이 다른 코드를 분리해서 두 개의 클래스로 만들었다.
* 그중에서 바뀔 수 있는 쪽의 클래스는 인터페이스를 구현하도록 하고, 다른 클래스에서 인터페이스를 통해서만 접근하도록 만들었다. 관계를 느슨하게 만든다.
* 자신의 책임 자체가 변경되는 경우 외에는 불필요한 변화가 발생하지 않도록 막고, 자신이 사용하는 외부 오브젝트의 기능은 자유롭게 확장하거나 변경할 수 있도록 한다. 개방폐쇄원칙
* 오브젝트가 생성되고 여타 오브젝트와 관계를 맺는 작업의 제어권을 별도의 오브젝트 팩토리를 만들어 책임을 전가했다.
* 전통적 싱글톤 패턴 구현 방식의 단점을 극복한 싱글톤 레지스트리 컨테이너
* 설계시점과 코드에는 클래스와 인터페이스 사이의 느슨한 의존관계만 만들어놓고, 런타임시에 실제 사용할 구체적인 의존 오브젝트 DI컨테이너의 도움으로 주입받아서 다이나믹한 의존관계를 만들었다.

###스프링이란 어떻게 오브젝트가 설계되고, 만들어지고, 어떻게 관계를 맺고 사용되는지에 관심을 갖는 프레임워크임을 항상 기억하자.
###하지만 오브젝트를 어떻게 설계하고, 분리하고, 개선하고, 어떤 의존관계를 가질지 결정하는 일은 개발작의 역할이며 책임이다.
###스프링은 단지 원칙을 잘 따르는 설계를 적용하려고 할 때 필연적으로등장하는 번거로운 작업을 편하게 할 수 있도록 도와주는 도구일뿐!