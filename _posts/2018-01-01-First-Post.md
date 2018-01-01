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
However this code inplies that Employees and Customers are the same
