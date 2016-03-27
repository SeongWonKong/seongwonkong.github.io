---
layout: post
title:  "Android Fragment"
date:   2016-03-24 14:34:22
categories: archieve
comments: true
---

# Fragment
## 소개
* 액티비티 보다 `작은 화면 단위`입니다.
* 디자인 타임에 배치가 결정되는 액티비티에 비해 실행 중에도 추가, 제거, 교체가 가능한 `동적`이고 유연한 방법을 제공합니다.
* 자신만의 레이아웃, 동작, 생명 주기를 갖는 `독립적인 모듈`입니다.
* 여러 액티비티에서 `재사용`이 가능합니다.
* 프래그먼트를 사용하려면 조각에 해당하는 레이아웃과 클래스가 한 세트로 필요합니다.(물론 xml 레이아웃말고 코드로 하고, 내부클래스를 사용하면 다르지만)
* 디자인 타임에 배치하는 프래그먼트의 경우 layout에 id가 필수입니다.

## 생명주기
 프래그먼트 추가 - onAttach - onCreate - onCreateView - onActivityCreated - onStart - onResume - 프래그먼트 실행 - onPause - onStop - onDestroyView - onDestroy - onDetach - 프래그먼트 파괴
 
 와 같은 흐름을 갖습니다. (백스택 저장 없을 경우)
 
 조금 복잡한데 간단하게 설명하면
 * **onAttach(Activity activity)** 는 액티비티에 프래그먼트가 처음 부착될 때 호출됩니다.
 이 때 호스트, 즉 주인 액티비티가 인수로 넘어오는데 이 부분에 대한 처리가 필요하면 구현하면 되지 않을까요?(제 생각... 실제로는 잘 구현하지 않는거 같네요)
 
*  **onCreate(Bundle savedInstanceState)** 는 프래그먼트가 생성될 때 호출됩니다.
 참고로 이 단계에서는 아직 호스트 액티비티가 초기화 중인 상태라서 액티비티의 컨트롤을 안전하게 참조할 수 없습니다. 
 
 
*  ** View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)** 은 프래그먼트의 UI를 처음 그릴 때 호출되며 프래그먼트는 자신의 레이아웃을 생성한 후 루트 뷰를 리턴합니다. 즉, 첫번째 인자로 넘겨 받은 inflater로 전개하여 프래그먼트가 생성되고, 두번째 인자인 container 영역에 자리잡게 됩니다.
 

* **onActivityCreated(Bundle savedInstanceState)** 은 프래그먼트도 잘 자리잡고, 액티비티가 완전히 초기화 되었을 때 호출됩니다. 액티비티가 완성되어서 액티비티의 위젯 등을 참조, 조작할 수 있습니다.


* **onPause()** 메소드는 프래그먼트가 정지될 때 호출되는데, 이 단계에서 영구 정보를 저장합니다.


* **onSaveInstanceState(Bundle outState)** 은 onPause와 함께 호출되는데 임시정보를 저장해놓습니다. 프래그먼트가 재생성(onCreateView)될 때 세번째 인자로 이 임시정보가 넘어옵니다.

이렇게 설명해도 좀 복잡한데.. 아래에 중요한 콜백들은 조금 더 정리해보겠습니다.

--------------------------

 `onCreatView 콜백`에서는 inflater를 이용하여 레이아웃을 전개해서 뷰를 만듭니다. 
뷰를 만들고, 여러 이벤트 핸들러를 포함시킨 뒤, 완성된 뷰를 리턴합니다. 조각의 완성이라고 할 수 있습니다. 완성된 조각을 액티비티의 적절한 위치에 붙이는 역할은 시스템이 해줍니다.
(왜나하면 프래그먼트의 생명주기는 액티비티의 레이아웃 전개 과정에서 fragment 엘리먼트를 만나 name 속성에 명시된 클래스의 객체를 생성하며 시작하기 때문에 어디에 붙일지는 구지 안 알려줘도 시스템이 잘 알고 있죠.)

레이아웃을 전개할 때 마지막 인자로 false를 넣는 것도 이런 이유에서 입니다.(true로 해도 상관없는데 그러면 같은 위치에 중복으로 붙이는 것이죠.)

그럼 container(프래그먼트가 부착될 부모뷰)는 인자로 왜 있느냐..하면 레이아웃 파라미터 참조용으로 사용하기 때문입니다.(레이아웃 파라미터라 하면 layout_width, height 등을 말합니다. 실제로 미리 정의한 XML 레이아웃을 전개하지 않고, 직접 자바코드로 생성하면 코드상에서 container는 쓸 일이 없습니다. 자바 코드 중에 
```
LinearLayout.LayoutParams param = new LinearLayout.LAyoutParams(
	LinearLayout.LayoutParams.WRAP_CONTENT,
    LinearLayout.LayoutParams.WRAP_CONTENT);
```
처럼 레이아웃 파람을 직접 설정하기 때문이죠.)

또한 주의할 점 한 가지는 onCreatView에서는 getActivity().findViewById로 만들고 있는 프래그먼트의 차일드를 참조할 수 없습니다. 이제는 뻔한 얘기지만, onCreatView가 완성된 조각을 리턴해야지만 프래그먼트가 액티비티에 추가가 되는데, 아직 추가되기 전이죠. 프래그먼트 소속의 위젯이 아직 액티비티의 차일드로 등록도 되지 않아서 getActivity를 통해서 참조할 수 없습니다.

만약, 프래그먼트의 차일드에 대한 참조가 필요하면 액티비티가 완성된 `onActivityCreated 콜백`에서 안전하게 검색할 수 있습니다.
이 때, getActivity()로 호스트 액티비티에서 차일드를 검색하면 루트에서부터 찾아서 느리기 때문에 getView()를 통해서 프래그먼트의 루트에서부터 찾는다면 더 빠르겠죠?

그리고 아이디가 중복되는 프래그먼트가 존재할 경우 getActivity()에서 찾으면 같은 아이디의 두번째 프래그먼트는 검색을 못합니다. 따라서 getView()를 통해 자신의 루트 프래그먼트를 찾은 후 차일드를 검색하는 것이 좋습니다.

가장 이상적인 방법은 레이아웃 전개 직후 차일드 위젯을 검색해서 핸들러 등을 등록하는 것입니다! 


## 프래그먼트 관리자(Fragment Manager)
프래그먼트는 실행 중 편집이 가능합니다. 편집 작업은 FragmentManager를 통해 합니다.
```
getFragmentManager()
```
를 통해 Activity, Fragment 어느 곳에서나 메니저 객체를 얻을 수 있습니다.

프래그먼트를 관리/편집 하려면 프래그먼트를 검색해야합니다.
**아래는 FragmentManager의 메소드들입니다.**

```
Fragment findFragmentById(int id)
Fragment findFragmentByTag(String tag)
```
여기서 첫번째는 프래그먼트의 id 또는 부모의 id 모두 가능합니다.(단, 부모의 id일 경우 첫 번째 차일드만 검색이 가능하죠)
tag는 android:tag="태그" 또는 실행 중 생성된 프래그먼트에 붙였던 tag로 검색합니다.
또한, 백스택에 저장된 프래그먼트까지 검색합니다. 없을 경우 null을 리턴합니다.

```
FragmentTransaction beginTransaction()
```

----------------
이 메소드를 호출하면 편집 트랜잭션이 시작됩니다.  여기서 리턴되는 fragmentTransaction 객체의 메소드를 이용하여 프래그먼트를 편집합니다.
**아래는 FragmentTransaction의 메소드들입니다.**

```
FragmentTransaction add(int containerViewId, Fragment fragment[, String tag])
FragmentTransaction add(Fragment fragment, String tag)
```
부모 뷰에 프래그먼트를 추가합니다. 태그명을 붙이면 앞서 말한 findFragmentByTag로 검색할 수 있습니다.

```
FragmentTransaction remove(Fragment fragment)
FragmentTransaction replace(int containerViewId, Fragment fragment[, String tag])
FragmentTransaction show(Fragment fragment)
FragmentTransaction hide(Fragment fragment)
int commit()
```
직관적인 메소드들은 생략합니다.


## 인수전달
프래그먼트가 생성될 때 어떤 정보를 전달하여 생성하고 싶을 때 사용합니다.
가령 '공성원' 이라는 이름을 어플리케이션 실행 중에 프래그먼트 내의 TextView에 보여주고 싶을 때, Bundle 객체에 담아서 프래그먼트 내부에 저장합니다.
```
-----버튼 클릭 이벤트 내부 -----
/* 전달하려는 값을  자신을 생성하는  정적 메서드(newInstance)로 넘겨 줍니다 */
FragmentManager fm = getFragmentManager();
FragmentTransaction tr = fm.beginTransaction();
String name = mEditText.getText().toString(); // 공성원
TestFragment fragment = TestFragment.newInstance(name);
tr.add(부모뷰ID, fragment, "TagName");
tr.commit();

-----TestFragment 클래스 내부 정적 메서드------
/* 자기 자신의 인스턴스를 만들며 인수를 저장해 놓습니다 */
public static TestFragment newInstance(String name) {
	TestFragment tf = new TestFragment();
    
    Bundle args = new Bundle();
    args.putString("name", name);
    tf.setArguments(args);
    
    return tf;
}

-----TestFragmemt 클래스의 onCreateView-----
/* add 를 통해 동적으로 프래그먼트를 액티비티에 더할 때.. 정확하게는 프래그먼트의 UI가 만들어지는 과정에서 저장해논 인수를 꺼내서 씁니다 */
...
Bundle args = getArguments();
if(args != null) {
	name = args.getString("name");
}
....
```


## 백스택
```
FragmentTransaction addToBackStack(String name)
```
현재 프래그먼트 상태를 백스택에 저장합니다. 
액티비티의 스택과 유사하지만 사용자가 직접 관리해야됩니다. Back버튼을 누르면 스택 최상위 프래그먼트를 꺼냅니다.


----------------
`안드로이드 프로그래밍 정복 마시멜로판`(김상형 저) 책을 참고했습니다.