---
layout: post
title:  "�ȵ���̵� Parcelable"
date:   2016-03-21 14:34:22
categories: archieve
comments: true
---


# Parcelable
parcelable�� ����, �ٷ��̸� ���մϴ�.
�ȵ���̵忡�� ��ü�� ���� ������ ���·� �����ϴ� ������.
���ݴ� �ڼ��� ������ Parcel��ü�� ����  `���μ����� ���`�� ���� �޽��� ������ġ�Դϴ�.
������ Bundle�� �ٷ��� ����� ����� �����ؼ� `���� ���μ����� ���ǰ� ������ ���� �� ����`���� ����մϴ�.

�ڹ��� Serialize ����� ������ parcelable�� �ӵ� �鿡�� �����մϴ�.
�ڼ��� �������� [��ũ](http://aroundck.tistory.com/2477)�� �����ϼ���.

���� �ռ���
���� 2���� ��ǥ�� ���� Vertex Ŭ������ �����غ��� �մϴ�.
�ڵ� �󿡼� �� ��ǥ�� ArrayList�� ��û �����Ұǵ�, �̰� ��°�� �����Ϸ� �մϴ�.
```
class Vertex {
	private float x, y;
	Vertex(floast ax, float ay) {
        x = ax;
        y = ay;
    }
}
```

�ȵ���̵忡�� Parcelable ��ü�� ����Ϸ���
1. �����Ϸ��� ��ü�� Ŭ�������� `Parcelable �������̽��� ����`�ؾ� �մϴ�.
2. �� �� �ݵ�� 2���� �޼ҵ带 �������־�� �մϴ�.
	```
    int describeContents()
    void writeToParcel(Parcel dest, int flags)
    ```
    
    `ù��° �޼ҵ�`�� å�� ���ϸ� '������ ����� �����ϴ� ��Ʈ ����ũ���� �����ϴµ� Ư���� ����� ������� ������ `0�� �����ϸ� �ȴ�`.' ��� �մϴ�.
    å �������� �߰����� Ȱ������ ������ �ʾƼ� ���� �� �˰ԵǸ� �����̰ڽ��ϴ�.
    
    `�ι�° �޼ҵ�`�� ��ü�� �ʵ带 dest�� ����ϴ� �κ��Դϴ�.
    ������ �����ϸ� �����ϰ� ���� ��ü�� �ɹ��������� `parcel�� ����`�ϴ� ������.
    
    �� �ΰ����� ���ϸ�
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
	�Դϴ�. �⺻������ Parcel�� write*, read* ���� �޼ҵ带 �����մϴ�.
    ���� Ÿ�Ե��� �迭�� �������ϴ� �޼ҵ嵵 �����մϴ�.
    * ����� �����ʹ� `�������` �а� ����մϴ�. Parcel�� ���� ���μ����� ����� ���� ������ �����͸� ����ȭ�ؼ� �����մϴ�. ������Ÿ�Ը��� �����ϴ� ũ�Ⱑ �޶������� ������ ���������� �ؾ��մϴ�.(���� setDataPosition�� ���� �޼ҵ带 ���ؼ� Ư�� �κ��� �ڷ�� �ٷ� �� �� ������..)
    
3. ���������� CREATOR ��� ���� ��ü�� �ʿ��մϴ�.
	�̴� ����� ������ Parcel�� ���� �о�鿩�� ��ü�� �����ϴ� ������ �մϴ�.
    ���� �ΰ��� �޼ҵ嵵 �� �ʿ��մϴ�.
    ```
    T createFromParcel(Parcel source)
    T[] newArray(int size)
    ```
    ù��° �޼ҵ�� Parcel�ٷ��̷κ��� ������ �о�ͼ� ��ü�� �ϳ� �����մϴ�.
    �ι�° �޼ҵ�� Parcel�ٷ��̸� �ѹ� ���캸��, �ʿ��� ��ŭ �迭�� �����ϴ� ������.
    �ΰ����� ����, Parcel�� ���� �о�� ���� ���� ����� ��ü�� ������ŭ �迭�� �����س���, �ű⿡ �ٷ��̸� �ϳ��� � ����ִ´ٰ� ������ �� �ֽ��ϴ�.
    
    ������� �����ϸ�...
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
�̷����ϴ�.

���� �ռ� ���Ѵ�� Bundle�� �ٷ��� ����� ����� �̿��ؼ� �ְ� ���� �ϸ� �˴ϴ�.

------------

�뷫���� �帧�� �����غ���..
Parcelable �ٷ��̷� ����� ���ؼ��� �� ���� ������ parcelable�� �����Ǿ���Ѵ�.
���� �ش� ��ü�� Parcelable �������̽��� �����ؾ� �Ѵ�.
�� �� �ݵ�� 2���� �޼ҵ�(describeContents, writeToParcel)�� �����ؾ��Ѵ�.(�ٷ���ȭ)
���� ������� �ٷ��̸� ����� ���ؼ� ������ü�� CREATOR�� �ʿ��ѵ� ���⼭�²ٷ����� ũ��(newArray)�� �˾ƺ���, createFromParcel�� ���� �ٷ��̸� ���� ��ü�� �����´�.