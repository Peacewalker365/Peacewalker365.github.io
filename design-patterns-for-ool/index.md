# Design Patterns For OOL

# Design Patterns For OOL

### Terms

Association

**Aggregation** implies a relationship where the child can exist independently of the parent. Example: Class (parent) and Student (child). Delete the Class and the Students still exist.

**Composition** implies a relationship where the child cannot exist independent of the parent. Example: House (parent) and Room (child). Rooms don't exist separate to a House.



## SOLID Design Principles

S: Single-responsibility principle

O: Open-closed principle

L: Liskov's substitution principle

I: Interface segregation principle

D: Dependency inversion principle







There are mainly 3 catagories for design patterns.

1. Creational: Diff ways to create objs
2. Structural: Relationships btw objs
3. Behavioural: Interactions and comm. btw objs

There are more undocumented design patterns existed.

We can use design patterns to comm. with dev at a more abstract lvl.

And make reusable, extensible, maintainable software.





## Strategy Pattern

The strategy pattern defines a family of algorithms and encapsulate each one, and makes them interchangeable.Strategy let the algorithms very independently found the clients who use it. (If you want to change the algorithems, you don't need to change the client.)

https://www.youtube.com/watch?v=OMPfEXIlTVE&t=1782s

**Null Object Pattern**

**The key idea of strategy pattern: Compostion & Injection**

**Inheritance is useful for sepecialization not for sharing codes!**

Name the concepts using spread sheet.

| Class       | data | ???      | ????      |
| ----------- | ---- | -------- | --------- |
| House       | data | nothing? |           |
| RandomHouse | data | shuffle  |           |
| EchoHouse   | data | ??       | duplicate |

nothing? is not a nothing, it is a algorithm.

??? is the order!

Order is the role of this, and nothing? is the default order.

So, we can do this:

```ruby
class House
  attr_reader :data
  def initialize(orderer: DefaultOrder.new)
    @data = orderer.order(DATA)
  end
  #...
end
class DefaultOrder
  def order(data)
    data
  end
end

class RandomOrder
  def order(data)
    data.shuffle
  end
end

```

Similarly, we can get this:

| Class       | data | order   | format    |
| ----------- | ---- | ------- | --------- |
| House       | data | default | default   |
| RandomHouse | data | shuffle | default   |
| EchoHouse   | data | default | duplicate |

We can make the design neat and elegant by analysis.

Just creating subclasses for every object having some unique behavior is not the solution.

Now we realize the same goal with less code and zero duplication.

We isolate the differences and name the concepts and define that role and APIs, then inject into the object. Make good use of the "**Active Nothing**" (do not search for the null everytime, let the object stand in for that since "**Nothing is always something**").

**Composition + Denpendency Injection**



For example, there are abstract class Dog with several methods. Then there are 2 subclasses sharing some methods that the super class does not have. Here we should use interface instead of inheritance. And then implement the interface differently and apply accordingly to each subclass of dog.

When you use inheritance, though the subclass don't have that much methods, the instance of the subclass can be large since all hidden methods from the super class are also there.



**Pro**:

1. Prevents the conditional statements. (switch, if, else…)
2. The algorithms are loosely coupled with the context entity. They can be changed/replaced without changing the context entity.
3. Very easy extendable.

**Con**:

1. Clients must know existence of different strategies and a client must understand how the Strategies differ
2. It increases the number of objects in the application.

## Observer Pattern

Push vs. Poll

Think about that we now have a wether station and a client like a phone. Now we want to know if there are any updated news.

One way to do this is through polling. The phone will simply query the data periodically. It consumes a lot of resources to keep asking for the changes. (There might be millions of clients need the data)

The other way is to let the phone subscribe the weather station which will notify the phone whenever the data get updated. And this solvement leans to the concept of pushing.

The clients are observers, and the weather station is the observerable.

The weather station has the following methods:

add(...)

remove(...)

notify()



The client has the following methods:

update()



We should notice that in this case, the client may "has-a" weather station. And we know that usually this relationship only appear on the higher abstraction level rather than the implementation level. The reason is that we have to pass in the weather station object into the constructor of the client so that when the client call the update() method where no arguments are passed in, it know how to access the data from the weather station.

In the notify, we iterate through the list of subscribers and call the update() method of it.





There are a lot of vairiants of ob pattern. You can do it in push&pull, poll&push, and etc. Please google it if you forget.

For the push&poll, we need to pass in the type of the observable into the constructor of the observer so that the implemented instances can access the data of the corresponding observables without creating abstractions for every type of the observables.

Check Christopher Okhravi's youtube vids if you forget everything.

Pro:

Con: You might end up with complicated nested event-driven structure, a event send signal to a event that send a sigal back to the first event, and there could be more complex structure in the middle.



## Decorator Pattern

You have an object, we send a message like speak() to the object and expect a string returned. If we want to change the object's behavior without change the code of the object itself, we can use a decorator to wrap it up, which is like an outer object.

Now we send the message to the decorator and it sends the mesage to the object. Then the object send the string to the decorator, the decorator processes the result and send it to us.

**The decorator has a component and is a component.**

The decorators is the same type of its inside while the decorators are also the wrappers of the inside.

We say the decorator is a its component because we want to treat it as it was the same thing.

We say the decorator has a its component because you want to send the message downward and get them back.

**Each level of the decorators attach additional responsibilities to an object dynamically.**

**Decorator pattern provides flexibility for the subclasses to extend functionalities.** At runtime, you cannot create a object combine subclass A and B but you can use decorator X and Y to combine them.



We have a abstract class Beverage, and we also have bunch of subclasses such as Decaf, Mocha, Steamed, Milk..,

We cannot create a subclass for each of the combination since those would be too many and each of them just do some tiny work. Most importantly, they are unmanageable.

**Attempt**: All these options can be treated as bools. And we can include those bools throught setters in the superclass. 

**Probs**: 

1. But this is extremely unexpressive, and when we have such conditionals we will put ourselves in a place that needs more conditionals! (conditional breed)

2. And if we want to expand the options, we have to modify the superclass so that many other subclass will also get this functions inherited they'll never use, which disobeys the SOLID principles.

**Return a boolean usually invoke a conditional statement else where in your code.**

Attempt: Now introducing => Decoraaaaators

`Abstract class Beverage` has `getDress()` `cost()`

`class Decaf`

`class Espresso`

`Addon Decorator` is a Beverage and has a Beverage. And it has `getDress()`

And it has bunch of implementations like `CaramelDeco`, `SoyDeco`

No matter how many decorators you want, just add it as an additional outer wrapper.

Notice here the Addon Decorator has the getDress() function so that it acts like the Beverage, and the fact that it is the outer decorator make it has a Beverage inside.

Please note that in this case the decorator pattern may not be the best implementation but this case illustrates the concepts very well.



The value passing downward and upward is through recursion. For example, the cost() here. The decorator calls the cost() recursively so that when it reaches the base case, which is when it reaches the cost() of Decaf or Espresso, the value get returned from inside to outside.

There might be multiple decorators but each of them just think it is wrapping up only one thing.

```pseudocode
abstract class Beverage{
	public abstract int cost();
}

abstract class AddonDecorator: Beverage{
	public abstract int cost();
}

class Espresso: Beverage{
	public abstract int cost(){
		return 1;	
	}
}

class CaramelDeco: AddonDecorator{
	Beverage beverage;
	int price = 2;
	public CaramelDeco(Beverage b) {
		this.beverage = b;
	}
	public int cost() {
		return this.beverage.cost() + this.price;
	}
}
```



Frankly, iteration pattern would be more properate to calculate the total cost, since the cost() in each decorator behaves the same.

A better example would be buffterAlignmentDeco --> ...non-deco or deco... --> bufferInputDeco --> inputString

This is better since:

1. They are working on the same type: String
2. Each decorator behaves differently





## Factory Pattern


