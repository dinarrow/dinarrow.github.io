---
theme: jekyll-theme-hacker
layout: default
title: Reducing  inheritance ( and code) with Type Safety using Namespaces
date: 2018-01-01 00:00:00
---

# Reducing  inheritance ( and code) with Type Safety using Namespaces

As Sean Parent has famously said ["Inheritance Is The Base Class of Evil"](https://channel9.msdn.com/Events/GoingNative/2013/Inheritance-Is-The-Base-Class-of-Evil)

Similarly the C++ Core Guidlines say we should prefer [Concrete and Regular Types](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#SS-concrete)

This leads us to the alternative of Composition. However composition can require more (often repedative) code.

For example consider these three base classes.


```c++

class Id
{
pubilc:
  int value(){return id_};
private:
  int id_;
};

class Name
{
public:
std::string value(){ return name_;}
private:
std::string name_;
};

class Address
{
public:
std::string value(){return address_;}
private:
std_string address_;
};
```

and then these two composite (dervived) structures
```c++
struct Employee
{
Id id;
Name name;
Address address;
}:

struct Customer
{
Id id;
Name name;
Address address;
};
```
This code is obviously repedative (in breach of the DRY guideline) and using inheritance we would probably create the obvious Person struct.

```c++
struct Person
{
Id id;
Name name;
Address address;
};
```

resulting in the much shorter

```c++
struct Employee : public Person
{
}
struct Customer : public Person
{
};
```
However this code implies that Employees and Customers are the same i.e. both people. But consider a Wholesaler who Customers are (always?) Businesses. Then we need another struct

```c++
struct Business
{
Id id;
Name name;
Address address;
};

struct Customer : public Business
{
};
```
and we are repeating ourselves again. So again we create another struct (Details for want of a better name)

```c++
struct Details
{
Id id;
Name name;
Address address;
};
```

and derive Person and Business

```c++
struct Person : public Details
{
};

struct Business : public Details
{
};
```
Again we have minimized the code but again we have linked four types that may in fact have no connection. Employee, Customer, Person and Business all derive from Details so we can have

```c++
Employee alice;
Employee bob;
Customer acme;
Person charlie;
Business delta;
```
and we can write

```c++
alice = bob = acme = charlie = delta; // Warning Splitting!!

or

alice.id = bob.id = acme.id = charlie.id = delta.id;
```
which is not particularly Type Safe.

Now lets add another detail such as Next_of_kin. Businesses do not have next of kin so we should not put it in the Details structure and it goes in the Person instead.

```c++
struct Person : public Detail
{
	std::string next_of_kin;
};
```
and the result is that we can have an Employee with a Next_of_kin detail but at a different level of inheritance to the other details.

Further lets us add  Birthdate and Age, again to the Person


```c++
struct Person : public Detail
{
	std::string next_of_kin;
	Date birth_date;
	Age age;
};
```
but `birth_date` and `age` are an Invariant so one should calculate from the other
```c++
struct Person : public Detail
{
	std::string next_of_kin;
	Date birth_date;
	Age age(){ now()-birth_date;}
};
```

and the Guidelines say that Invariants should be in classes, not structs, and classes should not expose their variable directly leading to


```c++
class Person : public Detail
{
public:
	std::string next_of_kin(){return next_of_kin_;};
	Date birth_date() { return birth_date_;}
	Age age(){ now()-birth_date_;}

private:
	std::string next_of_kin_;
	Date birth_date_;
};
```

All well and good but now when we use Employee

```c++
Employee alice;

auto name = alice.name;
auto next_of_kin = alice.next_of_kin();
```

we access two apparently similar details in two different way. Not only that Employee is now a `struct` with member functions and Perosn is a `class` with member variables.

So we add the necessary functions to Person and make Emplyee a class


```c++
class Person : public Detail
{
public:
	Id id(){ return Detail::id;}
	Name name(){return Detail::name;}
	Address address(){return Detail::address;}
	std::string next_of_kin(){ return next_of_kin;}
	Date birth_date() { return birth_date_;}
	Age age(){ return now()-birth_date_;}

private:
	std::string next_of_kin_;
	Date birth_date_;
};

class Employee :: public Person
{
};
```

but we can still write
```c++
alice = bob = acme = charlie = delta; // Warning Splitting!!
```
but not
```c++
alice.id = bob.id = acme.id = charlie.id = delta.id;
```
 as we need setters as well

```c++
class Person : public Detail
{
public:
	Id id(){ return Detail::id;}
	Name name(){return Detail::name;}
	Address address(){return Detail::address;}
	std::string next_of_kin(){ return next_of_kin_;}
	Date birth_date() { return birth_date_;}
	Age age(){ return now()-birth_date_;}

	void id(Id id value){ Detail::id= value;}
	void name(){return Detail::name= value;}
	void address(){return Detail::address= value;}
	void next_of_kin(std::string value){next_of_kin_=value;}
	void birth_date() { birth_date_= value;}

private:
	std::string next_of_kin_;
	Date birth_date_;
};
```
allowing equivalent non-Type Safe
```c++
charlie.id(delta.id);
acme.id=charlie.id();
bob.id(acme.id);
alice.id(bob.id());
```

which is a confusing mess I think all would agree.

So the 'solution' here is make everything a class and access all member variables by member functions (getters and setters) so we can write

```c++
charlie.id(delta.id());
acme.id(charlie.id());
bob.id(acme.id());
alice.id(bob.id());
```
which is still not Type Safe.

The final Inheritance code in the simplified world is
```c++
class Id
{
pubilc:
  int value(){return id_};
private:
  int id_;
};

class Name
{
public:
	std::string value(){ return name_;}
private:
	std::string name_;
};

class Address
{
public:
	std::string value(){return address_;}
private:
std_string address_;
};


class Detail
{
public:
	Id id(){ return id_;}
	Name name(){return name_;}
	Address address(){return address_;}

	void id(Id id value){ id_= value;}
	void name(Name value){name_= value;}
	void address(Address value){address_= value;}

private:
	Id id_
	Name name_;
	Address_ address_;
};

class Business : public Details
{
};

class Person : public Detail
{
public:
	std::string next_of_kin(){ return next_of_kin_;}
	Date birth_date() { return birth_date_;}
	Age age(){ return now()-birth_date_;}

	void next_of_kin(std::string value){next_of_kin_=value;}
	void birth_date() { birth_date_= value;}

private:
	std::string next_of_kin_;
	Date birth_date_;
};

struct Employee : public Person
{
}
struct Customer : public Business
{
};
```

This in the end leads us to believe if we are to use Inheritance we should never uses `struct` (as we may have to add an Invariant in the future) and it is classes all the way down ( Hello Java!!).

Note we still have details declared at different inheritance levels ( so we can spend our time playing hunt the declaration/definition more so if we add a virtual somewhere) and we are not Type Safe.

To make the code Type Safe we need to stop code such as this

```c++
Employee alice;
Customer acme;
	
alice.id(acme.id());
```
which implies the value of the ids ( to be unique) come for the same source ( say a GUID)nd even then is not Type Safe.

So `alice.id()` and `acme.id()` need to return different type implying that they are different classes.

But how do we declare those classes?

We could declare a `Person_id` and a `Business_id` but if we have another class derived from Person
```c++
class Contractor : public Person
{
};
```
Then we end up with the same issue. So we need different top level Ids ( Note if there is a database involved then you are almost guaranteed to require top level Ids). Thus we have

```c++
class Employee_id : Id
{
};

class Customer_id: Id
{
};
```

and either
```c++
class Employee: public Person, public Employee_id // Diamond Inheritance!!
{
};

or

class Customer : public Business
{
public:
	Customer_id id(){ return id_;}  // Shading!!
	void id(Customer_id value){ id_=value;}
private:
	Customer_id id_;
};
```
The 'Fix' in both cases is to remove Id from Details (and add `Person_id` and `Bussiness_id' classes) or give the Employee and Customer Id functions different names i.e. employee_id() and customer_id(). This second solution however means that we redundantly have to write
```
Employee alice;
	
auto id = alice.employee_id();
```

and unless we hide it 
```
auto id = alice.id()  // What does this mean??
```
So the first fix then

```c++
class Detail
{
public:
	//Id id(){ return id_;}
	Name name(){return name_;}
	Address address(){return address_;}

	void id(Id id value){ id_= value;}
	void name(Name value){name_= value;}
	void address(Address value){address_= value;}

private:
	Id id_
	Name name_;
	Address_ address_;
};
```
But Type-wise Name and Address are no different to Id ( each can have different business rules) so we do the same with them.Detail is now empty and pointless ( ad by recursion so is Business) and we get

```c++
class Employee : public Person
{
public:
	Employee_Id id(){ return id_;}
	Employee_Name name(){return name_;}
	Employee_Address address(){return address_;}

	void id(Employee_Id id value){ id_= value;}
	void name(Employee_name value){name_= value;}
	void address(Employee_address value){address_= value;}

private:
	Employee_Id id_
	Employee_Name name_;
	Employee_Address address_;

};

class Customer
{
public:
	Customer_id id(){ return id_;}
	Customer_name name(){return name_;}
	Customer_address address(){return address_;}

	void id(Customer_Id id value){ id_= value;}
	void name(Customer_name value){name_= value;}
	void address(Customer_address value){address_= value;}

private:
	Customer_Id id_
	Customer_Name name_;
	Customer_Address address_;
};
```
Which basically brings us back to where we started with duplicate code!!

So what happens if we introduce a namespace

```c++
namespace employee
{
class Employee_id : Id
{
};

class Employee_name : Name
{
};

class Employee_address : Address
{
};

class Employee : public Person
{
public:
	Employee_id id(){ return id_;}
	Employee_name name(){return name_;}
	Employee_address address(){return address_;}

	void id(Employee_Id id value){ id_= value;}
	void name(Employee_name value){name_= value;}
	void address(Employee_address value){address_= value;}

private:
	Employee_Id id_
	Employee_Name name_;
	Employee_Address address_;

};
}
```

Immediately we can see there is a lot of redundancy in terms of text such as employee::Employee_id for example. This is easily removed

```c++
namespace employee
{
class Id : ::Id
{
};

class Name : ::Name
{
};

class Address : ::Address
{
};

class Employee : public Person
{
public:
	Id id(){ return id_;}
	Name name(){return name_;}
	Address address(){return address_;}

	void id(Id id value){ id_= value;}
	void name(Name value){name_= value;}
	void address(Address value){address_= value;}

private:
	Id id_
	Name name_;
	Address address_;

};
}
```
and the Employee class looks a lot cleaner. The other classes however still appear redundant as they are just simple wrappers to obtain a unique type. An alternative mechanism to do this is a Template. If we simply turn the base classes into templates


```c++
template < typename T>
class Id
{
pubilc:
  int value(){return id_};
private:
  int id_;
};

template < typename T>
class Name
{
public:
	std::string value(){ return name_;}
private:
	std::string name_;
};

template < typename T>
class Address
{
public:
	std::string value(){return address_;}
private:
std_string address_;
};
```

we can write

```c++
namespace employee
{

class Employee : public Person
{
public:
	Id<Employee> id(){ return id_;}
	Name<Employee> name(){return name_;}
	Address<Employee> address(){return address_;}

	void id(Id<Employee> id value){ id_= value;}
	void name(Name<Employee> value){name_= value;}
	void address(Address<Employee> value){address_= value;}

private:
	Id<Employee> id_
	Name<Employee> name_;
	Address<Employee> address_;

};
}
```
although the Employee class is more cluttered. We can do better if we forward declare the class and put the base classes in their own namespace
```c++
namespace Base
{
template < typename T>
class Id
{
pubilc:
  int value(){return id_};
private:
  int id_;
};

template < typename T>
class Name
{
public:
	std::string value(){ return name_;}
private:
	std::string name_;
};

template < typename T>
class Address
{
public:
	std::string value(){return address_;}
private:
std_string address_;
};
}

namespace employee
{
class Employee;
using Id = Base::Id<Employee>;
using Name = Base::Name<Employee>;
using Address = Base::Address<Employee>;

class Employee : public Person
{
public:
	Id id(){ return id_;}
	Name name(){return name_;}
	Address address(){return address_;}

	void id(Id id value){ id_= value;}
	void name(Name value){name_= value;}
	void address(Address value){address_= value;}

private:
	Id id_
	Name name_;
	Address address_;

};
}
```
which is nice and clean. 

We can do similar for Customer


```c++
namespace customer
{
class Customer;
using Id = Base::Id<Customer>;
using Name = Base::Name<Customer>;
using Address = Base::Address<Customer>;

class Customer
{
public:
	Id id(){ return id_;}
	Name name(){return name_;}
	Address address(){return address_;}

	void id(Id id value){ id_= value;}
	void name(Name value){name_= value;}
	void address(Address value){address_= value;}

private:
	Id id_
	Name name_;
	Address address_;

};
}
```
But we can go yet further. Notice that if we ignore the remaining inheritance ( Person class) Employee and Customer differ only in those words and this is not required as the are in different namespaces. If we replace them both with the word 'Class' we get
```c++
namespace employee
{
class Class;
using Id = Base::Id<Class>;
using Name = Base::Name<Class>;
using Address = Base::Address<Class>;

class Class : public Person
{
public:
	Id id(){ return id_;}
	Name name(){return name_;}
	Address address(){return address_;}

	void id(Id id value){ id_= value;}
	void name(Name value){name_= value;}
	void address(Address value){address_= value;}

private:
	Id id_
	Name name_;
	Address address_;

};
}
namespace customer
{
class Class;
using Id = Base::Id<Class>;
using Name = Base::Name<Class>;
using Address = Base::Address<Class>;

class Class
{
public:
	Id id(){ return id_;}
	Name name(){return name_;}
	Address address(){return address_;}

	void id(Id id value){ id_= value;}
	void name(Name value){name_= value;}
	void address(Address value){address_= value;}

private:
	Id id_
	Name name_;
	Address address_;

};
}
```
and the code in each namespace is exactly the same ( except for the inheritance).

Now we have to get our hands a little bit dirty and do something not normally regarded as 'correct'. If we take the forward declarations and put then in a file.

```c++
// File: forward_declarations.inc

class Class;
using Id = Base::Id<Class>;
using Name = Base::Name<Class>;
using Address = Base::Address<Class>;

```
 and put the class definitions in another file
```c++
// File: class_definitions.inc

public:
	Id id(){ return id_;}
	Name name(){return name_;}
	Address address(){return address_;}

	void id(Id id value){ id_= value;}
	void name(Name value){name_= value;}
	void address(Address value){address_= value;}

private:
	Id id_
	Name name_;
	Address address_;

```
we can then write
```c++
namespace employee
{
#include "forward_delarations.inc"

class Class : public Person
{
#include "class_definitions.inc"
};
}

namespace customer
{
#include "forward_delarations.inc"

class Class
{
#include "forward_delarations.inc"
};
}
```
and if we have a Contractor provided by and Agency
```c++
namespace contractor
{
#include "forward_delarations.inc"

class Class : public Person
{
#include "class_definitions.inc"

public:
	Agency agency(){ return agency_;}
	void agency(Agency value){ agency_=value;}

private:
	Agency agency_;
};
}
```
Similarly we can replace the remaining inheritance with a composite class and some helper functions.
```c++
namespace contractor
{
#include "forward_delarations.inc"

class Class
{
#include "class_definitions.inc"

public:
	Agency agency(){ return agency_;}
	void agency(Agency value){ agency_=value;}

private:
	Agency agency_;

public:
	Person person(){ return person_;}
	void person(Person value){ person_=value;}

// helpers
	std::string next_of_kin(){return person.next_of_kin();};
	void next_of_kin(std::string value){ person.next_of_kin(value);}
	Date birth_date() { return person.birth_date();}
	void birth_date(Date value){person.birth_date(value);}
	Age age(){ person.age();}

private:
	Person person_;
};
}
```
Finally both the Agency and Person code can be shifted to their own include file to give
```c++
namespace contractor
{
#include "forward_delarations.inc"

class Class
{
#include "class_definitions.inc"
#include "agency.inc"
#include "person.inc"

};
}
```


So what have we ended up with?

 We have Type Safe classes with minimal code repetition requiring zero Inheritance. 
Classes can be concrete, allocated on the stack and be reasoned about locally.
There is almost no coupling between classes and an individual class can be modified or replaces without impact on surrounding code.
Behaviour unique to a class is obvious ( not in an include file) and changing a common behaviour ( variation of a Person for example) only requires the include file to be copied into the class ( and become obvious)  and be modified.

On the negative we have used #includes for code outside the headers and this is not by default handled well by editors or IDEs. Also what about other functions of Inheritance such as Polymorphism?

These benefits, disadvantage and questions I hope to explore further in later posts along with further expansions of this coding scheme.
