---
layout: post
title: "Unity3d Singleton 패턴을 사용하는 방법"
excerpt: "유니티에서 가장 많이 쓰이는 패턴 중 하나인 싱글톤 패턴에 대한 설명 및 코드"
date: "2017-02-04 20:07:46 +0900"
tags: [unity3d]
---

## 01. 싱글톤 패턴

싱글톤 패턴이란?

싱글톤 패턴은 가장 많이 알려진 패턴 중 하나입니다.

본질적으로 싱글톤은 자신의 단일 인스턴스만 생성될 수 있게 해주는 클래스이며 일반적으로 해당 인스턴스에 대한 간단한 액세스를 제공합니다.

일반적으로 유니티에서 매니저 클래스나 컨트롤러 클래스를 싱글톤으로 만들어서 관리해 주는 편입니다.

유니티에서의 싱글톤은 2가지 버전으로 구분할 수 있습니다.

1. C# 싱글톤 버전
2. Monobehaviour 버전

기존에는 싱글톤 패턴을 사용할 때 내가 편한 방식으로 사용을 했었고 가장 간단한 방식을 선호 했었는데. 조금은 안전한 싱글톤 패턴을 사용하기 위해서 고민이 필요할 것 같다.

### 1. C# 싱글톤 버전

#### 스레드 세이프 하지 않은 버전

```
// Bad code! Do not use!
public sealed class Singleton
{
    private static Singleton instance=null;

    private Singleton()
    {
    }

    public static Singleton Instance
    {
        get
        {
            if (instance==null)
            {
                instance = new Singleton();
            }
            return instance;
        }
    }
}
```

#### 간단히 구현된 스레드 세이프 한 버전

```
public sealed class Singleton
{
    private static Singleton instance = null;
    private static readonly object padlock = new object();

    private Singleton()
    {
    }

    public static Singleton Instance
    {
        get
        {
            lock (padlock)
            {
                if (instance == null)
                {
                    instance = new Singleton();
                }
                return instance;
            }
        }
    }
}
```

스레드 세이프한 방법을 사용하자.

#### 제네릭을 사용한 방법

```
using System;  
using System.Reflection;  

public class Singleton<T> where T : class  
{   
   private static object _syncobj = new object();   
   private static volatile T _instance = null;  

   public static T Instance
   {  
       get  
       {  
           if (_instance == null)  
           {  
               CreateInstance();  
           }  
           return _instance;  
       }  
   }  

   private static void CreateInstance()  
   {   
      lock (_syncobj)  
      {  
          if (_instance == null)  
          {  
              Type t = typeof(T);  

              // Ensure there are no public constructors...  
              ConstructorInfo[] ctors = t.GetConstructors();  
              if (ctors.Length > 0)  
              {  
                  throw new InvalidOperationException(String.Format("{0} has at least one accesible ctor making it impossible to enforce singleton behaviour", t.Name));  
              }  

              // Create an instance via the private constructor  
              _instance = (T)Activator.CreateInstance(t, true);  
           }  
       }      
   }   
}  
```

리플렉션 기능을 사용하여 생성된 인스턴스 수를 체크 후 1개 이상이면 익셉션 에러를 띄운다.

### 2. Monobehaivour 버전

#### 간단한 방법

```
using UnityEngine;
using System.Collections;

public class Singleton: MonoBehaviour
{
    public static Singleton instance = null;              //Static instance of GameManager which allows it to be accessed by any other script.

    //Awake is always called before any Start functions
    void Awake()
    {
        //Check if instance already exists
        if (instance == null)
        {        
            //if not, set instance to this
            instance = this;
        }
        //If instance already exists and it's not this:
        else if (instance != this)
        {        
            //Then destroy this. This enforces our singleton pattern, meaning there can only ever be one instance of a GameManager.
            Destroy(gameObject);    
        }   

        //Sets this to not be destroyed when reloading scene
        DontDestroyOnLoad(gameObject);
    }
}
```

#### Generic을 사용한 방법

```
using UnityEngine;
using System.Collections;

/// <summary>
/// Be aware this will not prevent a non singleton constructor
///   such as `T myT = new T();`
/// To prevent that, add `protected T () {}` to your singleton class.
/// As a note, this is made as MonoBehaviour because we need Coroutines.
/// </summary>
public abstract class Singleton<T> : MonoBehaviour where T : MonoBehaviour
{
    private static T _instance = null;
    private static object _syncobj = new object();
    private static bool appIsClosing = false;

    public static T Instance
    {
        get
        {
            if (appIsClosing)
                return null;

            lock (_syncobj)  
            {  
                if (_instance == null)
                {
                    T[] objs = FindObjectsOfType<T>();

                    if (objs.Length > 0)
                        _instance = objs[0];

                    if (objs.Length > 1)
                        Debug.LogError("There is more than one " + typeof(T).Name + " in the scene.");

                    if (_instance == null)
                    {
                        string goName = typeof(T).ToString();
                        GameObject go = GameObject.Find(goName);
                        if (go == null)
                            go = new GameObject(goName);
                        _instance = go.AddComponent<T>();
                    }
                }
                return _instance;
            }
        }
    }

    /// <summary>
    /// When Unity quits, it destroys objects in a random order.
    /// In principle, a Singleton is only destroyed when application quits.
    /// If any script calls Instance after it have been destroyed,
    ///   it will create a buggy ghost object that will stay on the Editor scene
    ///   even after stopping playing the Application. Really bad!
    /// So, this was made to be sure we're not creating that buggy ghost object.
    /// </summary>
    protected virtual void OnApplicationQuit()
    {
        // release reference on exit
        appIsClosing = true;
    }
}
```

사용 방법

````
public class Manager : Singleton<Manager> {
	protected Manager () {} // guarantee this will be always a singleton only - can't use the constructor!

	public string myGlobalVar = "whatever";
}
````

Generic을 사용한 방법에서는 Singleton을 상속받은 클래스에서는 생성자를 반드시 protected로 선언을 해서 외부에서는 생성이 되지 않게 막는다.

그리고 Singleton 클래스의 applicationIsQuitting 변수의 경우 별로 깨끗하지 않은 방법인것 같지만.. 유니티 에디터 상에서 갑자기 나가버리는 경우에 에러가 발생하는 경우라 반드시 필요한 변수이다. 이렇게 로직을 처리할 시에는 instance가 null이 나오는 경우가 생기므로 null 처리를 따로 처리해줘야 된다.

만약에 씬이 변환 되도 파괴되지 않은 싱글톤을 만들고 싶을 시에는 상속받은 클래스의 awake 함수에 아래와 같이 선언한다.

DontDestroyOnLoad(this.gameObject); 의 함수를 실행시켜 주자.

### 참조

- [Implementing the Singleton Pattern in C#](http://csharpindepth.com/Articles/General/Singleton.aspx)
- [Unity3d wiki Singleton](http://wiki.unity3d.com/index.php/Singleton)
- [Singleton : Implementation in unity3d c#](http://www.unitygeek.com/unity_c_singleton/)
- [Writing the Game Manager](https://unity3d.com/kr/learn/tutorials/projects/2d-roguelike-tutorial/writing-game-manager)
