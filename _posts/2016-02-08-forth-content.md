---
layout: post
title:  "스프링 TEST에 대한 이해"
date:   2016-02-08 14:34:22
categories: archieve
comments: true
---
지난 주는 프로젝트의 테스트 커버리지 90% 달성을 위해 달린 한 주였다.<br>
물론 설 명절 전까지 달성을 못했지만..<br>
지난 주에 했던 삽질과 지금까지 이해한 내용들을 정리해보고자 한다.<br>

## JUnit 확장 클래스 지정
`@RunWith(SpringJUnit4ClassRunner.class)`<br>
테스트 클래스 위에 위의 애노테이션을 붙이면 JUnit이 테스트를 진행하는 중에
테스트가 사용할 애플리케이션 컨텍스트를 만들고 관리하는 작업을 진행해준다.(토비의 스프링 185p)<br>
즉, SpringJUnit4ClassRunner이라는 확장 클래스를 지정하지 않으면 설계도에 해당하는 애플리케이션 컨텍스트를 사용할 수 없고.. @Autowired VotreeDao votreeDao; 등과 같이 테스트를 할 때 자동으로 값을 주입받을 수 없게 된다. 
이렇게 애플리케이션 컨텍스트를 관리하도록 하면 테스트 클래스에서
여러개의 테스트(@Test)가 진행되어도, 애플리케이션 컨텍스트는 매번 만들어지지 않는다.<br>
즉, 아래에 설명하겠지만 `독립적인 각각의 테스트`들이 단 한번 생성된 application context를 공유하게 된다.
## ApplicationContext 위치 지정
{% highlight java %}
@ContextConfiguration(locations = {
		"classpath:spring/applicationContext.xml",
		"classpath:spring/servlet-context.xml"
      })
{% endhighlight %}
애플리케이션 컨텍스트.. 즉 설계도의 위치를 지정하는 애노테이션이다.<br>
설계도 없이는 DI를 못한다 물론.<br>

여기서 기초적이지만 (테스트와는 연관없는) 한가지 의문이 들었다. 지금까지 프로젝트를 하며 `/`로 시작하면 절대경로 아니면 상대경로라고 이해를 하고 있었다. 위에 애플리케이션 컨텍스트의 위치를 보면
`classpath:spring/applicationContext.xml` 이라고 지정한 것을 볼 수 있다.<br>
테스트를 진행하고 있는 곳에 spring 폴더가 있지 않은데.. 왜? 라는 생각이 들었다.<br>
혹시하는 마음에 `/spring/applicationContext.xml`로도 바꾸어 봤는데 잘 동작했다.<br>
이상해서 이것저것 시도해보던 중에 classpath가 보였다.<br>
classpath의 path가 바로 `src/main/resources` 였던 것이다. 기초가 부족하니 이런걸로 고민을 하는구나 싶었다.<br>
조금더 엄밀히 말하면.. `src/main/resources` 내에 있는 파일들은 빌드를 하게 되면 `WEB-INF/classes` 로 오게된다.(이 부분은 프로젝트 properties에서 Deployment Assembly를 확인하면 알 수 있다.)<br>
classpath는 이 classes 경로에서 해당 파일을 찾는다고 할 수 있다. 즉, src/main/resources에 있는 것을 찾는다고 이해할 수 있다.(뭔가 명쾌하지가 않은데.. 추후 더 공부해서 보완하겠다.)

## @Before, @After, @BeforeClass, @AfterClass
토비의 스프링 vol.1 180~183p에 자세히 나오지만 몇 가지 요약하면<br>
1. @Test 메소드의 개수만큼 오브젝트를 항상 새로 만든다.<br>
2. @Test 메소드 실행 전, 후에 @Before, @After이 각 테스트와 독립적으로 실행된다.<br>
	* 간단히 말하면 이전 테스트의 결과로 나온 인스턴스 변수 등 모든 것이 다음 테스트와는 무관하다.<br>
3. 2번의 이유로 @Test 메소드의 실행 순서는 그때그때 다를 수 있고, 달라도 전혀 상관없다.<br>
4. @BeforeClass, @AfterClass는 테스트 클래스 전체에 한 번만 실행되는 스태틱 메소드이다.<br>
	* 애플리케이션 컨텍스트와 같은 경우 @BeforeClass에서 한 번만 만들고 공유하면 되지만.. 사실 위에 언급한 JUnit 확장 클래스를 사용하는 편이 낫다.

# 아래 3가지는 토비의 스프링 205p에 나온 몇 가지 학습 테스트에 대해 공부한 결과이다.
## 1. @Autowired로 가져온 빈 오브젝트와 getBean()으로 가져온 빈은 정말 동일한가?
{% highlight java %}
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {
        "classpath:/spring/applicationContext.xml",
        "classpath:/spring/servlet-context.xml"
      })
public class LearningTest {
    @Autowired
    VotreeDao votreeDao;
    
    @Autowired
    ApplicationContext context;

    @Test
    public void 싱글톤테스트1(){
        System.out.println("싱글톤테스트1:"+votreeDao);
    }
    
    @Test
    public void 싱글톤테스트2(){
        System.out.println("싱글톤테스트2:"+votreeDao);
    }
    
    @Test
    public void getBean테스트(){
        System.out.println("getBean테스트:"+context.getBean(VotreeDao.class));
    }
    
    @Test
    public void getDataSource테스트(){
        System.out.println(context.getBean(BasicDataSource.class).getDriverClassName());
        System.out.println(context.getBean(BasicDataSource.class).getUrl());
        System.out.println(context.getBean(BasicDataSource.class).getUsername());
    }
}
{% endhightlight %}
JUnit 확장 클래스를 이용하여 애플리케이션 컨텍스트를 지정하여 만들어진 빈 오브젝트가 votreeDao에 주입된 것<br>
그리고 context에 주입된 애플리케이션 컨텍스트에서 getBean으로 가져온 votreeDao는 같음을 알 수 있었다.<br>
`싱글톤테스트1:com.toast.votree.dao.VotreeDao@7c1e2a2d`<br>
`싱글톤테스트2:com.toast.votree.dao.VotreeDao@7c1e2a2d`<br>
`getBean테스트:com.toast.votree.dao.VotreeDao@7c1e2a2d`<br>

## 2. 스프링은 정말 싱글톤 방식으로 빈의 오브젝트를 만드는가?
또한, 빈이 싱글톤 스코프를 갖는 것도 확인할 수 있다. 테스트는 각각 독립적으로 실행되는데 VotreeDao 오브젝트의 값이 항상 동일함을 알 수 있다.<br>(그런데.. 이것이 JUnit 확장 클래스를 이용해 애플리케이션 컨텍스트를 한번 만 생성하게 해둬서 동일한거지, 싱글톤임을 확인하는지는 확실하게 모르겠다.. 이 부분도 추후 알아보고 보완해야겠다..)

## 3. XML에서 설정한 프로퍼티 값이 빈에 잘 주입되는가?
applicationContext.xml에 직접 등록한 프로퍼티 값이 아래와 같다.
{% highlight html %}
<bean id="dataSourceHooked" class="org.apache.commons.dbcp2.BasicDataSource" destroy-method="close">
	<property name="driverClassName" value="com.mysql.jdbc.Driver" />
	<property name="url" value="jdbc:mysql://10.161.223.32:13306/toast" />
	<property name="username" value="rookie_1" />
</bean>
{% endhighlight %}

`com.mysql.jdbc.Driver`<br>
`jdbc:mysql://10.161.223.32:13306/toast`<br>
`rookie_1`<br>

빈에 잘 주입되었다는 것을 확인할 수 있다.


