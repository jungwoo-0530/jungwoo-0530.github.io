---
title:  "Overriding vs Overloading"
excerpt: ""

categories:
  - java
tags:
  - [Java, overriding, overloading]
 
date: 2021-11-01
last_modified_at: 2021-11-01

---



### overriding overloading

- overriding

  - 상위 클래스의 메서드와 이름과 용례(signature)가 같은 함수를 하위 클래스에 재정의하는 것을 말한다.

  - ```java
    public abstract class Shape {
      public void printMe() { System.out.println("Shape"); }
      public abstract double computeArea();
    }
    public class Circle extends Shape {
      private double rad = 5;
      @Override // 개발자의 실수를 방지하기 위해 @Override(annotation) 쓰는 것을 권장
      public void printMe() { System.out.println("Circle"); }
      public double computeArea() { return rad * rad * 3.15; }
    }
    public class Ambiguous extends Shape {
      private double area = 10;
      public double computeArea() { return area; }
    }
    ```


    

- overloading

  - 두 메서드가 같은 이름을 갖고 있으나 인자의 수나 자료형이 다른 경우를 말한다.

  - ```java
    public double computeArea(Circle c) { ... }
    public double computeArea(Circle c1, Circle c2) { ... }
    public double computeArea(Square c) { ... }
    ```

    