---
layout: post
title: "Design Patterns"
author: "Mohamed Alrawy"
---

In real life we face many problems, some of which have faced our ancestors and with the frequent recurrence of old and seek to solve them there is an ideal solution to those recurring problems, so we do not need to think much when faced, only need to transfer their experiences and follow their methods.
<br>
As well as the software in some problems there is a known style of solution that is considered to be the salvation of many experiences and called these solutions design patterns.
<br>
In this blog I will explain what is design patterns in simple way, and give examples simplifies them.
<br>
Design patterns are a way of thinking or a method to solve logical problems in programming or to improve the performance of the program or to facilitate writing code.
<br>
This method does not necessarily have to be the best and is not imposed on every programmer to use it, but it keeps the experiences of many programmers and the result of many research and applications. 
<br>
There are many types of Design patterns, but we can't give a specific number, because the patters in constant renewal and in continuous increase . when there is a new problem or a solution to old problem, and this solution was good and used by programmers then as a solution to their similar problems, may become a new design pattern. In the same program we can use more than one design to solve more than one problem.
<br>
So we go back to the starting point and say that this is not mandatory and there is no specific approach so it depends on the choices of the programmer and his style of solution and thinking.
<br>
We can classify the design patterns into three categories and fall under each category many types. We will give a definition for each category and mention a software practical problems, and a good way to solve them with suitable design pattern.
<br>
## 1 - Creational Patterns
These design patterns provide a way to create objects while hiding the creation logic, rather than instantiating objects directly using new operator. This gives program more flexibility in deciding which objects need to be created for a given use case.
#### Example:
![casher]({{ site.baseurl }}/img/1.png)
<br>
If you asked for create payment system, this system asked every user if he will pay cash or by debit card or by credit card what would you do ?   Will you design three classes and create three instances for every user and he choose suitable one ?  Of course it will take useless space
So the right solution in **factory pattern**
### Factory Pattern
<br>
![factory pattern]({{ site.baseurl }}/img/2.png)

{% highlight c# %}
interface factory
    {
 	void pay();
}
class Cash:Factory
    {
        public void pay()
        {
            Console.Write("pay cash");
        }
    }
 class Credit:Factory
    {
        public void pay()
        {
            Console.Write("pay credit");
        }
    }
 class Debit:Factory
    {
        public void pay()
        {
            Console.Write("pay debit");
        }
    }
class TemplatePattern
    {
        public Factory getClass(string payment_way)
        {
            if (payment_way.Equals("cash"))
                return new Cash();
            else if (payment_way.Equals("debit"))
                return new Debit();
            else 
                return new Credit();
        }
    }
{% endhighlight %}

## 2 - Structural Patterns
These design patterns concern class and object composition. Concept of inheritance is used to compose interfaces and define ways to compose objects to obtain new functionalities.
<br>
![Pizza]({{ site.baseurl }}/img/3.png)
#### Example:
If you want to make a system for a pizza restaurant, there is many types like chicken, beef, hot dog, cheese and so on. It's easy to design a class for every one and all inherit from pizaa Without any additions parent class, and for every order create an instance for required pizza. It's really easy .But if restaurant owner want to open new branch "create your own" what will you do as a developer ? Of course
 it's not right solution to design a class has all attributes there is too many useless attributes and takes large memory. so what is the solution?
Solution in **decorator pattern**
### Decorator Pattern
<br>
![Decorator Pattern]({{ site.baseurl }}/img/4.png)

{% highlight c# %}
public abstract class BasePizza
    {
        protected double myPrice;
        public virtual double GetPrice()
        {
            return this.myPrice;
        }
    }
public abstract class DecoratedPizaa :BasePizza
{ 
       public abstract String getDescription(); 
}
public class Chiecken extends DecoratedPizaa 
{ 
     basePizza base; 
    public Checken (BasePizaa base) { 
        this.base = base; 
    } 
    public override double GetPrice()
      {
            return (this.base.GetPrice() + this.myPrice);
      }
{% endhighlight %}

## 3 - Behavioral Patterns
These design patterns are specifically concerned with communication between objects.	
<br>
![ovserver]({{ site.baseurl }}/img/5.png)
#### Example:
If we have Auction and there is publisher and attendances, if any one raise his sign and add his Amount proposed. we want to tell every one about new amount how can we implement this ?
Let’s see another example .
<br>
![scatters]({{ site.baseurl }}/img/6.png)
<br>
#### Another Example:
Let's say there is values table for any statistics values and we want to represent them as a graphs or scatters, and we want to implement them in object oriented. it's too easy if was static values but what if it was dynamic? So we want to create dynamic scatters and changes when values in table changed . How can we implement this?
### Observer Pattern
<br>
![Observer Pattern]({{ site.baseurl }}/img/7.png)

{% highlight c# %}
public class Subject{
        private List<Observer> observers = new List<Observer>();
        private int state;
        public int getState()
            return state;
        
        public void setState(int state)
        {
            this.state = state;
            notifyAllObservers();
        }
        public void attach(Observer newObserver)
        {
            observers.Add(newObserver);
        }
        public void notifyAllObservers()
        {
            foreach (Observer observer in observers)
            {
                observer.update();
            }
}
}
public abstract class Observer
    {
        public subject subject;
        abstract public void update();
    }
class Observer1 : Observer
    {
        public Observer1(subject s)
        {
            this.subject = s;
            this.subject.attach(this);
        }
        public override void update()
        {
            Console.Write(this.subject.getState());
        }
    }
{% endhighlight %}

### summary
A summary of the blog design patterns are excellent methods to coordinate the code and solve problems and sometimes help in the speed of development which makes the program more flexible and valuable so must be studied well so that you can choose the required pattern carefully
I hope you have benefited
<br>
### referances
1- https://www.tutorialspoint.com/design_pattern/ 
2- Head First Design Pattern book



