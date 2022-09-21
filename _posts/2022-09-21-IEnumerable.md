---
title: IEnumerator, IEnumerable 인터페이스
date: 2022-09-20 21:10:00
categories: [Language, C#]
tags: [ienumerable,iEnumerator]  
---
유니티 코루틴를 사용하며 여러개의 코루틴을 사용하기 위해 List< IEnumerator> 로 루틴을 호출하며 사용했는데 한번 코루틴이 진행되면 Current가 계속 WaitForSeconds에 남아 이후 코루틴을 재실행할 수 없는 일이 발생하였다.
MoveNext(), Reset()등을 사용해보고자 했으나 Reset은 접근이 되지않았고 MoveNext()를 진행해도 변화가 없어서 먼저 코루틴의 내부 구조와 IEnumerator 및 IEnumerable 에 대해 자세히 알아보고자한다.

## 📖  IEnumerator 인터페이스

제네릭이 아닌 컬렉션을 단순하게 반복할 수 있도록 지원하며 제네릭이 아닌 **모든 열거자의 기본 인터페이스**이다.

System.Collections.Generic.Stack<T>.Enumerator,System.Collections.Generic.List<T>.Enumerator 등 제너릭이 해당 인터페이스를 상속받는 것을 확인할 수 있다.

## 📖  IEnumerable 인터페이스

제네릭이 아닌 컬렉션에서 단순하게 반복할 수 있도록 지원하는 열거자를 노출하기 위한 인터페이스이다.

따라서 어떤 클래스를 컬렉션들과 같이 반복가능하게 만들고 싶다면 해당 클래스에대한 IEnumerator,IEnumerable를 상속받는 클래스들을 만들어 주면 된다.


## 👩‍💻 예제

1. 일반적인 클래스

```c#
using System; 
using System.Collections; 
 
public class Person { 
public Person(string fName, string lName) 
{ 
	this.firstName = fName; 
	this.lastName = lName; 
} 
	public  string firstName; 
	public  string lastName; 
}
```
2. 일반 클래스의 컬렉션 클래스

```c#
// 사람 객체에 대한 (Collection)집합
//IEnumerable 를 상속받아서 ForEach을 사용할 수 있음
public class People : IEnumerable 
{ 
	 private Person[] _people;
	 public People(Person[] pArray) 
	 { 
		 _people = new Person[pArray.Length]; 
		 for (int i = 0; i < pArray.Length; i++) 
	  		_people[i] = pArray[i]; 
	 } 
	// IEnumerator를 위한 GetEnumerator 구현
	IEnumerator IEnumerable.GetEnumerator() 
		{ return (IEnumerator) GetEnumerator(); }
	// PeopleEnum은 People클래스에 대한 IEnumerator 클래스이다.
	public PeopleEnum GetEnumerator() 
		{ return  new PeopleEnum(_people); }
 }

```
3. 일반 클래스의 컬렉션을 사용하기 위한 IEnumerator 클래스

```c#
//   IEnumerable을 구현할 때 IEnumerator도 같이 구현해야 쓸 수 있다.
public class PeopleEnum : IEnumerator
{
    public Person[] _people;

    // MoveNext()가 처음 호출되기 전까지 열거자(Enumerators)는 첫 번째 요소 앞에 배치된다.
    int position = -1;

    public PeopleEnum(Person[] list)
    {
        _people = list;
    }

    public bool MoveNext()
    {
        position++;
        return (position < _people.Length);
    }
	// Reset은 다시 위치를 -1로 변경함
    public void Reset()
    {
        position = -1;
    }

    object IEnumerator.Current
    {
        get
        {
            return Current;
        }
    }

    public Person Current
    {
        get
        {
            try
            {
                return _people[position];
            }
            catch (IndexOutOfRangeException)
            {
                throw new InvalidOperationException();
            }
        }
    }
}
```
4. forEach구문 사용

```c#
    static void Main()
    {
        Person[] peopleArray = new Person[3]
        {
            new Person("John", "Smith"),
            new Person("Jim", "Johnson"),
            new Person("Sue", "Rabon"),
        };
	//IEnumerable 컬렉션 집합인 People에서 foreach 구문이 사용 가능해진다. MoveNext() 를 호출하여 처음부터 배열의 끝까지 순회한다.
        People peopleList = new People(peopleArray);
        foreach (Person p in peopleList)
            Console.WriteLine(p.firstName + " " + p.lastName);
    }
```
## 🔗 Reference

[IEnumerator 인터페이스](https://learn.microsoft.com/ko-kr/dotnet/api/system.collections.ienumerator?view=net-6.0)
