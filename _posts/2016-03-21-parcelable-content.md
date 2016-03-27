---
layout: post
title:  "Android Parcelable 객체"
date:   2016-03-21 14:34:22
categories: archieve
comments: true
---

# Parcelable
parcelable은 소포, 꾸러미를 뜻합니다.
안드로이드에서 객체를 전달 가능한 형태로 포장하는 것이죠.
조금더 자세히 들어가보면 Parcel객체는 원래  `프로세스간 통신`을 위한 메시지 저장장치입니다.
하지만 Bundle이 꾸러미 입출력 기능을 제공해서 `같은 프로세스의 세션간 데이터 저장 및 복구`에도 사용합니다.

자바의 Serialize 방법도 있지만 parcelable이 속도 면에서 유리합니다.
자세한 차이점은 [링크](http://aroundck.tistory.com/2477)를 참조하세요.

설명에 앞서서
저는 2개의 좌표를 갖는 Vertex 클래스를 포장해보려 합니다.
코드 상에서 이 좌표를 ArrayList에 엄청 저장할건데, 이걸 통째로 저장하려 합니다.
```
class Vertex {
	private float x, y;
	Vertex(floast ax, float ay) {
        x = ax;
        y = ay;
    }
}
```

안드로이드에서 Parcelable 객체를 사용하려면
1. 포장하려는 객체의 클래스에서 `Parcelable 인터페이스를 구현`해야 합니다.
2. 이 때 반드시 2개의 메소드를 구현해주어야 합니다.
	```
    int describeContents()
    void writeToParcel(Parcel dest, int flags)
    ```
    
    `첫번째 메소드`는 책에 의하면 '마샬링 방법을 지정하는 비트 마스크값을 지정하는데 특별한 방법을 사용하지 않으면 `0을 리턴하면 된다`.' 라고 합니다.
    책 예제에서 추가적인 활용방법은 나오지 않아서 추후 더 알게되면 덧붙이겠습니다.
    
    `두번째 메소드`는 객체의 필드를 dest로 출력하는 부분입니다.
    간단히 설명하면 포장하고 싶은 객체의 맴버변수들을 `parcel로 포장`하는 것이죠.
    
    위 두가지를 더하면
    ```
    class Vertex implements Parcelable {
        private float x, y;
        Vertex(floast ax, float ay) {
            x = ax;
            y = ay;
        }
        
        public int describeContents() {
        	return 0;
        }
        
        public void writeToParcel(Parcel dest, int flags) {
        	dest.writeFloat(x);
            dest.writeFloat(y);
        }
    }
```
	입니다. 기본적으로 Parcel은 write*, read* 등의 메소드를 제공합니다.
    여러 타입들의 배열을 엑세스하는 메소드도 제공합니다.
    * 참고로 데이터는 `순서대로` 읽고 써야합니다. Parcel은 원래 프로세스간 통신을 위한 것으로 데이터를 직렬화해서 저장합니다. 데이터타입마다 차지하는 크기가 달라서접근은 무조건 순차적으로 해야합니다.(물론 setDataPosition과 같은 메소드를 통해서 특정 부분의 자료로 바로 갈 순 있지만..)
    
3. 마지막으로 CREATOR 라는 정적 객체가 필요합니다.
	이는 저장된 정보를 Parcel로 부터 읽어들여서 객체를 생성하는 역할을 합니다.
    다음 두개의 메소드도 꼭 필요합니다.
    ```
    T createFromParcel(Parcel source)
    T[] newArray(int size)
    ```
    첫번째 메소드는 Parcel꾸러미로부터 정보를 읽어와서 객체를 하나 생성합니다.
    두번째 메소드는 Parcel꾸러미를 한번 살펴보고, 필요한 만큼 배열을 생성하는 것이죠.
    두가지를 보면, Parcel로 부터 읽어올 때는 먼저 저장된 객체의 개수만큼 배열을 생성해놓고, 거기에 꾸러미를 하나씩 까서 집어넣는다고 생각할 수 있습니다.
    
    여기까지 정리하면...
```
    class Vertex implements Parcelable {
        private float x, y;
        Vertex(floast ax, float ay) {
            x = ax;
            y = ay;
        }
        
        public Vertex(Parcel in) {
            x = in.readFloat();
            y = in.readFloat();
            boolean temp[] = new boolean[1];
            in.readBooleanArray(temp);
            draw = temp[0];
        }
        
        public int describeContents() {
        	return 0;
        }
        
        public void writeToParcel(Parcel dest, int flags) {
        	dest.writeFloat(x);
            dest.writeFloat(y);
        }
        
         public static final Creator<Vertex> CREATOR = new Creator<Vertex>() {
            @Override
            public Vertex createFromParcel(Parcel in) {
                return new Vertex(in);
            }

            @Override
            public Vertex[] newArray(int size) {
                return new Vertex[size];
            }
        };
    }
```
이렇습니다.

이제 앞서 말한대로 Bundle의 꾸러미 입출력 기능을 이용해서 넣고 빼고 하면 됩니다.

------------

대략적인 흐름을 정리해보면..
Parcelable 꾸러미로 만들기 위해서는 그 내부 정보도 parcelable이 구현되어야한다.
따라서 해당 객체는 Parcelable 인터페이스를 구현해야 한다.
이 때 반드시 2개의 메소드(describeContents, writeToParcel)를 구현해야한다.(꾸러미화)
또한 만들어진 꾸러미를 열어보기 위해서 정적객체인 CREATOR가 필요한데 여기서는꾸러미의 크기(newArray)를 알아보고, createFromParcel을 통해 꾸러미를 원래 객체로 가져온다.




----------------
`안드로이드 프로그래밍 정복 마시멜로판`(김상형 저) 책을 참고했습니다.