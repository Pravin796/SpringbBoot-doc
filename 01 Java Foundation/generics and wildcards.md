What are Generics?
Generics allow classes, interfaces, and methods to work with different data types while maintaining type safety.

Before Java 5, collections stored only Object.

List list = new ArrayList();

list.add("Pravin");
list.add(10);

String name = (String) list.get(0); // Explicit casting
Problems:

No type safety

Runtime errors

Manual casting

Example:

String name = (String) list.get(1);
Output

ClassCastException
After Generics
List<String> list = new ArrayList<>();

list.add("Pravin");

// list.add(10); ❌ Compile-time error

String name = list.get(0);
Benefits

Type Safety

No Casting

Compile-time checking

Cleaner code

Generic Class
Instead of fixing the datatype, we use a placeholder.

class Box<T> {

    private T value;

    public void set(T value){
        this.value = value;
    }

    public T get(){
        return value;
    }
}
Now we can create different types.

Box<String> box1 = new Box<>();

box1.set("Hello");

System.out.println(box1.get());
Output

Hello
Another one

Box<Integer> box2 = new Box<>();

box2.set(50);

System.out.println(box2.get());
Output

50
Notice that the same class works for different data types.

What is T?
class Box<T>
T is just a convention.

You can use

class Box<X>
or

class Box<MyType>
But standard conventions are

Letter	Meaning
T	Type
E	Element
K	Key
V	Value
N	Number
Example

Map<K,V>
Here

Map<String,Integer>
means

K -> String
V -> Integer
Multiple Generic Types
class Pair<K,V>{

    K key;
    V value;

    Pair(K key,V value){
        this.key = key;
        this.value = value;
    }
}
Usage

Pair<Integer,String> p =
new Pair<>(1,"Pravin");
Generic Methods
Methods can also be generic.

public class Main{

    public static <T> void print(T value){

        System.out.println(value);

    }

}
Calling

print("Hello");

print(10);

print(5.6);
Output

Hello
10
5.6
Notice

<T>
comes before the return type.

Bounded Generics
Suppose you only want Numbers.

class Calculator<T extends Number>{

    T value;

}
Allowed

Calculator<Integer>

Calculator<Double>

Calculator<Float>
Not allowed

Calculator<String>
Compile Error

Multiple Bounds

<T extends Number & Comparable<T>>
Means

The type must

extend Number

implement Comparable

Why Generics are Invariant?
Consider

List<Integer>
Is it a subtype of

List<Number>
No.

This surprises many beginners.

Example

List<Integer> integers =
new ArrayList<>();

List<Number> numbers = integers; // ❌
Why?

Because then

numbers.add(3.14);
Now

integers
contains

Integer
Double
Impossible.

That's why Java prevents it.

Now comes Wildcards
Wildcards solve this limitation.

The wildcard symbol is

?
It means

"Some unknown type."

1. Unbounded Wildcard
List<?>
means

List of anything
Example

void print(List<?> list){

    for(Object obj : list){

        System.out.println(obj);

    }

}
Calling

print(List.of(1,2,3));

print(List.of("A","B"));

print(List.of(10.5,20.5));
Works for all.

Can we add?

list.add(10);
No.

Compile Error.

Why?

Because Java doesn't know the actual type.

Imagine

List<String>
If Java allowed

list.add(10);
Boom.

Now String list contains Integer.

Unsafe.

Upper Bounded Wildcard
<? extends Number>
Means

Any subtype of Number
Allowed

Integer

Double

Float

Long
Example

void sum(List<? extends Number> list){

}
Calls

sum(List.of(1,2,3));

sum(List.of(1.5,2.6));

sum(List.of(10L,20L));
All work.

Can we read?

Yes.

Number n = list.get(0);
Perfectly safe.

Can we add?

No.

list.add(10);
Compile Error.

Why?

Suppose

List<Integer>
comes in.

Adding

Double
would be wrong.

Java cannot guarantee safety.

Rule

extends

Read Only
Think:

I can safely read values as the parent type, but I can't safely write.

Lower Bounded Wildcard
<? super Integer>
Means

Integer or any parent
Allowed

List<Integer>

List<Number>

List<Object>
Example

void add(List<? super Integer> list){

    list.add(10);

    list.add(20);

}
This is safe.

Because

Integer
fits into

Integer

Number

Object
Can we read?

Only as

Object
Because Java doesn't know the exact type.

Object obj = list.get(0);
Not

Integer i = list.get(0);
Compile Error.

Rule

super

Write Only
The PECS Rule (Most Important Interview Question)
Joshua Bloch introduced a simple rule:

Producer Extends,


