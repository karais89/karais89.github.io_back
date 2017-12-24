---
layout: post
title: "C# 코딩 스탠다드"
excerpt: "C# 언어를 사용할 때 사용되는 코딩 스탠다드"
date: 2017-12-24 23:55:00 +0900
tags: [programming, unity3d]
---
* TOC
{:toc}


[김포프님 C# 코딩 스탠다드](https://docs.google.com/document/d/1ymFFTVpR4lFEkUgYNJPRJda_eLKXMB6Ok4gpNWOo4rc/edit#)

포프님이 올리신 C# 코딩 스탠다드의 번역 글입니다.

번역 자체는 구글 번역에서 이상한 부분은 제가 수정 하였습니다. 번역 자체의 문제가 있을 수 있습니다.

코딩 스탠다드는 코드 몽키 레벨에서는 왜 이렇게 작성했는지에 대한 설명을 할 필요도 없고 그냥 코딩 스탠다드에 주어진 내용 대로 코드를 짜면 된다고 합니다.

하지만 아래 45가지의 코딩 스탠다드에는 분명히 그렇게 해야 되는 이유가 존재 합니다.

꼭 아래의 코딩 스탠다드를 사용하여 코드를 작성하실 필요는 없습니다.

## 경험에 바탕을 둔 방법

1. 읽기 좋은 코드가 우선(대부분의 경우 사용 설명서가 항상 문서화되어야 함)
2. 그렇지 않은 이유가 있는 경우가 아니라면 IDE의 자동 형식 지정 방식을 따르십시오. (VisualStudio의 Ctrl+K+D)
3. 기존 코드로부터 배우기

## 참조

이 코딩 스탠다드는 이러한 코딩 표준에 의해 영감을 받는다.

- [Unreal engine 4 coding standard](https://docs.unrealengine.com/latest/INT/Programming/Development/CodingStandard/)
- [Doom 3 Code Style Conventions](ftp://ftp.idsoftware.com/idstuff/doom3/source/codestyleconventions.doc)
- [IDesign C# Coding Standard](http://www.idesign.net/downloads/getdownload/1985)

## 이름 지정 규칙 및 스타일

### 1. 클래스 및 구조체에 파스칼 케이스[^1] 사용

```
class SomeClass;	
struct SomeStruct;
```

### 2. 지역 변수 이름과 함수 매개 변수에 카멜[^2] 케이스 사용

```
void SomeMethod(int someParameter)
{
  int someNumber;
}
```

### 3. 모든 메소드 이름은 파스칼 케이스 사용

```
public uint GetAge()
{
  // function implementation...
}
```

### 4. 메소드 이름은 동사 + 명사 사용

```
public uint GetAge()
{
  // function implementation...
}
```

### 5. non-public 메서드면 post fix로 "Internal"을 붙인다

```
Private uint GetAgeInternal()
{
  // function implementation...
}
```

### 6. 상수는 ALL_CAPS_SEPARATED_BY_UNDERSCORE를 사용한다.

```
const int SOME_CONSTANT = 1;
```

### 7. namespaces는 파스칼 케이스를 사용한다.

```
namespace System.Graphics
```

### 8. boolean 변수 앞에는 b를 붙이고 프로퍼티에는 앞에 is를 붙인다.

```
bool bFired;    // for local variable
Private bool mbFired;    // for private class member variable
Public bool IsFired { get; private set; }
```

### 9. 인터페이스 앞에는 I를 붙인다.

```
interface ISomeInterface 
{

}
```

### 10. enum 앞에는 E를 붙인다.

```
public enum EDirection
{
  North,
  South
}
```

### 11. private 멤버 변수 앞에 m을 붙입니다. 나머지 멤버 변수에는 파스칼 케이스를 사용하십시오.

```
Public class Employee
{
  public int DepartmentID { get; set; }
  private int mAge;
}
```

### 12. 반환 값이있는 메소드는 반환 된 값을 설명하는 이름을 가져야합니다.

```
public uint GetAge();
```

### 13. 설명적인 변수 이름을 사용하십시오. 예를 들어 루프에 사용되는 사소한 변수가 아니라면 i 또는 e 대신 index이나 employee이 될 수 있습니다.

### 14. 두개의 문자만 있는 경우 머리 글의 모든 문자를 대문자로 구분합니다.

```
public int ID { get; private set; }
```

### 15. 두개의 문자 이상인 경우 머리 글자로 된 첫 번째 문자 만 대문자로 표시하십시오.

```
public string HttpAddress { get; private set; }
```

### 16. getter setter 함수보다 프로퍼티를 선호해라.

```
이거 대신에:
public class Employee
{
  private string mName;
  public string GetName();
  public string SetName(string name);
}

이렇게 사용해라:
public class Employee
{
  public string Name {get; set;}
}
```

### 17. 4 개의 스페이스와 동일하게 탭을 사용해라

### 18. 로컬 변수가 사용되는 첫 번째 행에 최대한 가깝게 선언하십시오.

### 19. 새로운 줄에 항상 여는 중괄호 "{" 를 넣으십시오.

### 20. scope에 단 하나의 줄이 있어도 중괄호를 추가하십시오.

```
if (bSomething)
{
  return;
}
```

### 21. 부동 소수점 값에 대한 정밀도 지정은 명시 적으로 double이 필요하지 않는 한 사용하십시오.

```
float f = 0.5F;
```

### 22. switch 문에는 항상 default가 있어야 합니다.

```
switch (number)
{
  case 0:
    ... 
    break;
  default:
    break;
}
```

### 23. case 문에 코드가없는 경우를 제외하고는 switch case에 대한 fallthrough 설명을 추가하십시오.

```
switch (number)
{
 case 0:
   DoSomething();
   //fallthrough
 case 1:
   DoFallthrough();
   break;
 case 2:
 case 3:
   DoNotFallthrough();
   break;
 default:
   break;
}
```

### 24. 스위치 문에서 default 문이 발생해야되지 않아야 되는 경우에는 항상 Assert(false)를 더해라. assert 구현에서, 이것은 릴리즈 빌드를위한 최적화 힌트를 추가 해라.

switch (type)
{
  case 1:
    ... 
    break;
  Default:
    Debug.Fail("unknown type");
    break;
}

### 25. 재귀 함수의 이름은 "Recursive"로 끝내라.

void FibonacciRecursive();

### 26. 클래스 변수와 메소드의 순서는 다음과 같아야합니다.

1. public variables
2. internal variables
3. protected variables
4. private variables
5. public methods
6. Internal methods
7. protected methods
8. private methods

### 27. 함수 오버로딩은 대부분의 경우 피해야합니다.

```
이거 대신에:
Anim GetAnim(int index);
Anim GetAnim(string name);

이렇게 사용해라:
Anim GetAnimByIndex(int index);
Anim GetAnimByName(string name);
```

### 28. 여러 개의 작은 클래스를 그룹화하는 것이 합리적이지 않으면 각 클래스는 별도의 소스 파일에 있어야합니다.

### 29. 파일 이름은 대문자와 소문자를 포함한 클래스의 이름과 동일해야합니다.

```
class Anim {}

Anim.cs
```

### 30. 너가 가지고 있는 주장에는 assert를 사용해라. (예, 모든 함수는 Debug.Assert( not null parameters) 를 가져야 한다.)

### 31. bigflag 열거 형의 이름은 Flags로 끝나야합니다.

```
public enum EVisibilityFlags
{
}
```

### 32. 기본 매개변수보다 오버로딩을 더 선호한다.

### 33. 기본 매개 변수가 사용되면 null, false 또는 0과 같은 자연스러운 상수로 제한하십시오.

### 34. 지역 변수 숨김은 허용하지 않는다.

```
public class SomeClass
{
  public int Count {get;set;}
  public void Func(int Count)
  {
    for (int count = 0; count != 10; ++count)
    {
      // Use Count
    }
  }
}
```

### 35. 항상  System.Collections의 컨테이너보다 System.Collections.Generic의 컨테이너를 사용하십시오. 순수 배열을 사용하는 것도 좋습니다.

### 36. var 키워드 대신 실제 타입을 사용하는 걸 선호합니다. Enumerator같은 경우에는 var를 사용할 수 있습니다.

### 37. 싱글톤 패턴이 아닌 static class를 사용해라.

### 38. async void 보다 async Task를 사용해라. async void는 오직 이벤트 핸들러에서만 사용해라.

### 39. 경계에서 외부 데이터의 유효성을 검사하고 데이터를 함수에 전달하기 전에 반환하십시오. 즉,이 시점 이후에 모든 데이터가 유효하다고 가정합니다.

### 40. 따라서 우리 함수 내부에서 예외를 던지지 마십시오. 이것은 경계에서만 처리되어야합니다.

### 41. public 함수에서는 특히 매개 변수에 null을 허용하지 않는 것이 좋습니다.

### 42. 만약 매개변수에 null을 사용해야 하는 경우라면, 매개변수 이름의 postfix로 OrNull을 붙이십시오.

### 43. 어떤 함수, 특히 public 함수에서는 null을 반환하지 않기를 바랍니다. 하지만 예외를 throw 하지 않도록 null을 반환해야 하는 경우가 있습니다.

### 44. 만약 함수에서 null을 반환해야 된다면, 함수 이름에 Postfix로 OrNull을 붙입니다.

```
public string GetNameOrNull();
```

### 45. 객체 initializer를 사용하지 말자. 대신에 명명 된 매개 변수와 함께 명시적 생성자를 사용합시다. 아래 두가지 예외 상황은 제외 합니다.

```	
a. 오브젝트가 한 곳에서만 작성된 경우. (예를 들어, 1 회의 DTO)
b. 객체가 소유 클래스의 정적 메서드 내에서 만들어 질 때 (예 : 팩토리 패턴)
```


[^1]: 첫 단어를 대문자로 시작하는 표기법. 예) BackgroundColor, TypeName, PowerPoint
[^2]: 각 단어의 첫문자를 대문자로 표기하고 붙여쓰되, 맨처음 문자는 소문자로 표기함. 예) backgroundColor, typeName, iPhone 