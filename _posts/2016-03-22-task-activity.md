---
layout: post
title:  "안드로이드 Task, Activity"
date:   2016-03-22 14:34:22
categories: archieve
comments: true
---

# Task

안드로이드의 실행 파일은 시작점이 따로 없습니다.
각 어플리케이션의 메니페스트에 런쳐로 등록된 엑티비티가 진입점 역할을 하게됩니다.
```
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
```
이런식으로 등록되죠.

물론 항상 그렇진 않습니다. 여러개의 액티비티가 런쳐에 등록될 수도있고, 액티비티가 없는 프로그램도 있습니다.


런쳐에 등록된 메인 엑티비티가 실행되면 메모리에 올라가고, 프로세스가되어 독립된, 격리된 채로 실행됩니다.
이렇게 생성된 하나의 테스크에서는 다른 패키지의 컴포넌트를 생성할 수 있습니다.
가령 부동산 앱에서 지도보기를 따로 구현하지 않고, 구현된 지도 액티비티를 부동산 앱의 테스크 내에서 실행할 수 있습니다.

즉, 동일한 목적을 위한 액티비티의 집합을 하나의 작업으로 묶어서 사용자에게 통합된 경험을 제공하는 것이 `태스크` 입니다.

# Activity

태스크 내에서 액티비티는 스택을 유지합니다. 즉, 푸쉬와 팝만으로 동작합니다.
태스크 내의 액티비티들은 하나의 덩어리로 생각하면 편합니다.
태스크가 비활성화되면 해당 액티비티들이 전부 백그라운드로 가고, 활성화되면 다시 그대로 복구됩니다.
만약, 앱을 시작해서 A라는 액티비티가 실행되고, B라는 액티비티로 전환하고, C라는 액티비티로 전환했다가, A라는 액티비티로 전환될 경우..
스택에는 A-B-C-A 가 되겠죠.
여기서 외부의 Camera 액티비티를 호출하게 되면
A-B-C-A-Camera
처럼 Task에 각 Activity들이 스택으로 쌓입니다. 물론 프로세스는 2개가 실행 중이겠죠. 단지 같은 테스크에 있을 뿐.

여기서 스택을 관리하는 방법 두가지를 안드로이드는 제공합니다.
`1. 매니페스트의 액티비티 속성을 조정하여 액티비티의 실행 위치나 회수를 제어(lauchMode)`
`2. 액티비티를 실행하는 인텐트의 Flag를 통해 액티비티의 동작을 제어(flag 제어)`


## 1. launchMode 4가지
매니페스트 파일에서 Activity에 설정합니다.
### standard
	디폴트로 액티비티가 호출될 때마다 새로운 인스턴스가 스택의 최상단에 생성됩니다. 이미 있던, 완전 새롭던 상관 없습니다.
    
### singleTop
	standard와 거의 동일하지만, 만약 새롭게 생성될 액티비티가 이미 최상단에 있을경우 새로 생성되지 않고 기존 인스턴스가 onNewIntent 메서드로 새 인텐트를 받습니다.
    
### singleTask
	singleTask가 설정된 액티비티를 실행하게 되면, 기존 태스크와 별개로 새로운 태스크에 액티비티가 쌓이게 된다.
    singleTop과 유사한 점은, 해당 액티비티를 또 실행하게되면 별개의 태스크가 또 생기지 않고, 좀전에 생긴 태스크의 onNewIntent 메서드가 호출되게 된다.
    대표적인 예로 웹브라우저같이 거대한 액티비티는 기존 태스크의 스택에 쌓이지 않고, 새로운 태스크가 생긴다.
    
### singleInstance
	singleTask과 유사하지만, 새롭게 생성된 태스크 위로 다른 액티비티가 쌓이지 못한다. 이 옵션이 설정된 액티비티는 항상 태스크의 루트에 존재하고 그 위로 아무것도 쌓이지 않는다. 가령 B라는 액티비티에 singleInstance가 설정된 경우, A에서 B를 열고, B에서 C를 열 경우 태스크는 3개가 된다.
    
##### singleTask와 singleInstance는 MAIN과 LAUNCHER 카테고리를 항상 가져야한다.
항상 루트에 존재하는 액티비티가 되기 때문에.. 하나의 온전한 태스크이므로 스스로 기동이 가능해야 한다.

## 2. 인텐트의 Flag
intent.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK); 와 같은 형식으로 사용한다.

#### FLAG_ACTIVITY_NEW_TASK
	액티비티를 새로운 태스크에서 시작한다.
    
#### FLAG_ACTIVITY_SINGLE_TOP
	액티비티에 singleTop 런치 속성을 임시로 부여하는 것이다. 즉, 스택의 최상위 액티비티가 재사용된다.
    
#### FLAG_ACTIVITY_CLEAR_TOP
	호출할 액티비티가 스택에 있다면, 그 위쪽의 모든 액티비티를 제거하고 해당 액티비티만 새로 생성한다.
    이 때, 이미 있던(호출할) 액티비티를 포함하여 위쪽을 모두 지운다.
    
#### FLAG_ACTIVITY_REORDER_TO_FRONT
	스택에 호출할 액티비티가 존재한다면, 해당 액티비티를 최상위로 끌어올립니다. 재정렬(Reorder)과 최상위(Front)로 가져옵니다.
    동일한 액티비티가 스택에 여러개 쌓이지 않게 합니다.
    
외에 엄청 많은데 http://developer.android.com/reference/android/content/Intent.html 참고.

## 기타

### 명시적 제거
	finish()를 호출하거나 Back 버튼을 누르면 스택에서 제거된다.
    
### activity의 설정을 통한 제거(매니페스트)
	1. clearTaskOnLaunch 를 true로 설정할 경우, 재시작할 때마다 루트를 제외한 모든 액티비티들을 종료한다.(루트에 지정하는 속성이다)
	2. finishOnTaskLaunch를 true로 설정할 경우, 해당 액티비티는 재시작할 때마다 스택에서 제거된다. 비활성화 되어있던 태스크를 다시 가져올 때 불필요한 화면은 제거할 때 주로 사용한다. 
	3. alwaysRetainTaskState 언제나 스택의 모든 액티비티를 유지한다.(루트 액티비티에 지정하는 속성이다)
    
