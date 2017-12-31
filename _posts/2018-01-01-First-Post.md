---
title: Reducing  inheritance ( and code) using namspaces
---

# Reducing  inheritance ( and code) using namspaces

As Sean Parent has famously said ["Inheritance Is The Base Class of Evil"](https://channel9.msdn.com/Events/GoingNative/2013/Inheritance-Is-The-Base-Class-of-Evil)

Similarly the C++ Core Guidlines say we should prefer [Concrete and Regular Type](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#SS-concrete)

This leads us to the alternative of Composition. However composition can require more (iften repedative) code.

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

and then these two composite (dervived) classes
```
class Employee
{
}:

class Customer
{
};
```
