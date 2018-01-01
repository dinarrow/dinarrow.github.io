---
title: Reducing  inheritance ( and code) using namspaces
---

# Reducing  inheritance ( and code) using namspaces

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
```
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

```
struct Person
{
Id id;
Name name;
Address address;
};
```

resulting in the much shorter

```
struct Employee : public Person
{
}
struct Customer : public Person
{
}
```
However this code implies that Employees and Customers are the same i.e. both people. But consider a Wholesaler who Customers are (always?) Businesses. Then we need another struct

```
struct Business
{
Id id;
Name name;
Address address;
}

struct Customer : public Business
{
}
```
and we are repeating ourselves again. So again we create another struct (Details for want of a better name)

```
struct Details
{
Id id;
Name name;
Address address;
};
```

and derive Person and Business

```
struct Person : public Details
{
}

struct Business : public Details
{
}
```
Again we have minimized the code but again we have linked four types that may in fact have no connection. Employee, Customer, Person and Business all derive from Details so we can have

```
Employee alice;
Employee bob;
Customer acme;
Person charlie;
Business delta;
```
and we can write

```
	alice = bob = acme = charlie = delta; // Warning Splitting!!
or
	alice.id = bob.id = acme.id = charlie.id = delta.id;
```
which is not particularly Type Safe.

Now lets add another detail such as Next_of_kin. Businesses do not have next of kin so we should not put it in the Details structure and it goes in the Person instead.

```
struct Person : public Detail
{
	std::string next_of_kin;
}
```
