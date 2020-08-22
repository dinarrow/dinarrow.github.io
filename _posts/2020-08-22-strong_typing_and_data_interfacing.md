---
theme: jekyll-theme-hacker
layout: default
title: Strong Typing and Data Interfacing
date: 2020-08-22  00:00:00
---

# Strong Typing and Data Interfacing

This relates back to my first post ["Reducing  Inheritance ( and code) with Type Safety using Namespaces"](First-Post.html) and a follow up [Type Safety and Simplicity](2018-09-10-type_safety_and_simplicity.html).

In the latter I looked at Templates and Generic Code and found them wanting outside the trivial and in Libraries such as the STL. I took this position based in part on the confusion that resulted from the complex error messages, particularly in the case of junior programmers and those inexperienced with the code. 

I attempted to demonstrate that using Namespaces, Strong Typing and Included Code Files could be used to produce more readable and understandable code that could be absorbed? locally and modified without significant and possibly unknown side effects.

In this post, now almost two years later, I will give a real world example of these concepts in action and at the same time demonstrate how the can be used along side Templates and Generic Code in a cooperative manner.


## Schema

Consider an SQL Table Schema for Personal Details such as this

```SQL

TABLE PersonalDetails 						-- ACTIONS: UPDATE ONLY										
	operID int NOT NULL,					-- The record ID from server 
	operName varchar(250) NOT NULL,			-- RO		The operators mobile login name
	operMobile varchar(50) NULL,			-- UPD 		Operators mobile number
	operFullName varchar(255) NULL,			-- UPD 		Full name
	operPhone varchar(20) NULL,				-- UPD 		Home phone number
	operEmail varchar(100) NULL,			-- UPD 		Personal email address
	operAddress varchar(255) NULL,			-- UPD 		Home address
	operPostal varchar(255) NULL,			-- UPD 		Postal address
	operPartnerName varchar(100) NULL,		-- UPD 		Partners name (or next of Kin)
	operPartnerPhone varchar(50) NULL,		-- UPD 		Partners phone
	operEmergencyName varchar(100) NULL,	-- UPD 		Emergency contact person
	operEmergencyPhone varchar(50) NULL,	-- UPD 		Emergency contact number/s
	operStatus varchar(10) NOT NULL,		-- RO 		Employment status
	operLicenses varchar(50) NULL,			-- RO 		Driver licence classes
	operStartDate varchar(10) NULL,			-- RO 		Date started work
	operKiwisaver bit NULL,					-- RO 		Belongs to Kiwisaver
	operIRDNumber varchar(20) NULL,			-- RO 		IRD number
	operLicenceNumber varchar(30) NULL,		-- RO 		Driver licence number
	operLicenceVers varchar(30) NULL,		-- RO 		Licence version number
	operBirthDate varchar(10) NULL,			-- RO 		DOB
	Team varchar(250) NOT NULL,				-- RO 		GAVINS team 
	Exemption varchar(250) NOT NULL			-- RO 		Any current truck driver exemption


```

Note that this is from a real example so there are some details such as naming conventions and columns that are not relevant to this discussion and will be ignored.

## Forward Declarations

The first step is to Forward Declare a Type for each column of the Schema in an appropriate Namespace. The reason for using Forward Declaration is limit the compilation overhead and declaration complexity that results from including headers within headers. By limiting the full declaration of Classes to headers in the Translation Unit Files (*.cpp) the required header for each Unit is clearer and more stable, not requiring includes that bleed through the header files.

```c++
  namespace personal_ns::details_ns
  {
    class Class;
    class Id;
    using Vector = std::vector<Class>;
    namespace name_ns
    {
      namespace name_ns
      {
        class Class;
      } // namespace name_ns
      using Name = name_ns::Class;
      namespace full_ns
      {
        class Class;
      } // namespace full_ns
      using Full = full_ns::Class;
    } // namespace name_ns
    namespace phone_ns
    {
      class Class;
    } // namespace phone_ns
    using Phone = phone_ns::Class;
    namespace mobile_ns
    {
    } // namespace mobile_ns
    using Mobile = mobile_ns::Class;
    namespace email_ns
    {
      class Class;
    } // namespace email_ns
    using Email = email_ns::Class;
    namespace address_ns
    {
      namespace physical_ns
      {
        class Class;
      } // namespace physical_ns
      using Physical = physical_ns::Class;
      namespace postal_ns
      {
        class Class;
      } // namespace postal_ns
      using Postal = postal_ns::Class;
    } // namespace address_ns
    namespace partner_ns
    {
      namespace name_ns
      {
        class Class;
      } // namespace name_ns
      using Name = name_ns::Class;
      namespace phone_ns
      {
        class Class;
      } // namespace phone_ns
      using Phone = phone_ns::Class;
    } // namespace partner_ns
    namespace emergency_ns
    {
      namespace name_ns
      {
        class Class;
      } // namespace name_ns
      using Name = name_ns::Class;
      namespace phone_ns
      {
        class Class;
      } // namespace phone_ns
      using Phone = phone_ns::Class;
    } // namespace emergency_ns
    namespace status_ns
    {
      class Class;
    } // namespace status_ns
    using Status = status_ns::Class;
    namespace licence_ns
    {
      namespace licences_ns
      {
        class Class;
      } // namespace licences_ns
      using Licences = licences_ns::Class;
      namespace number_ns
      {
        class Class;
      } // namespace number_ns
      using Number = number_ns::Class;
      namespace version_ns
      {
        class Class;
      } // namespace version_ns
      using Version = version_ns::Class;
    } // namespace licence_ns
    namespace kiwisaver_ns
    {
      class Class;
    } // namespace kiwisaver_ns
    using Kiwisaver = kiwisaver_ns::Class;
    namespace ird_ns
    {
      class Class;
    } // namespace ird_ns
    using IRD = ird_ns::Class;
    namespace team_ns
    {
      class Class;
    } // namespace team_ns
    using Team = team_ns::Class;
    namespace exemption_ns
    {
      class Class;
    } // namespace exemption_ns
    using Exemption = exemption_ns::Class;
    namespace date_ns
    {
      namespace start_ns
      {
        class Class;
      } // namespace start_ns
      using Start = start_ns::Class;
      namespace birth_ns
      {
        class Class;
      } // namespace birth_ns
      using Birth = birth_ns::Class;
      namespace update_ns
      {
        class Class;
      } // namespace update_ns
      using Update = update_ns::Class;
    } // namespace date_ns

  } // namespace personal_ns::details_ns
```


Take templates and generic code in general. Templated Generic code is one of the best features of C++. When you read articles and watch videos the power of templates is demostrated and can often seem miraculous when it comes to reducing the amount of code to be written and how much can be reused (ie STL). However to use templates beyond the trivial you rapidly need deep knowledge of how they work and the rules surrounding them particularly when something goes wrong.

Take a simple stream operator for example

```c++

template< typename T, typename S>
S& operator <<( T const & t, S& s)
{
    s.write(t.read());
	return s;
}
```

This is trivially simple and with a little template knowledge a junior coder can work out what it is trying to do. i.e. it takes two classes t and s, reads from t, writes to s and returns s. So T needs a read() function and S needs a write() function.

An experienced coder can write a template like this and the inexperience coder can then successfully use this template to simplify his code quite happily until something goes wrong. He is then faced with a wall of error information requiring a deep knowledge of the rules that implement the templated code before he can understand the problem. The issue can be quite trival ( the T used does not have a read() function) but the errors can still be extensive and hard to follow. The junior coder does his best to understand and starts reading about templates and generic programming but it has nothing to do with what he is trying to do or what he is already trying to learn. He probably has enough on his plate learning the codebase itself and not how the codebase actually works.

For less trivial templates the problem becomes exponentially worse even for a more experience coder. Sometimes something that has been used for a long time and extensively through the code suddenly does not compile because something in the codebase changes causes one of the many templating rules to now apply, or no longer apply. 

Now you have to figure out which of the rules it is and what the choices are to get it to work again. If you are lucky you can find some way to modify the instance you are working on or how it is used to get the code to compile again but if you have to change the template what happens to all the other instances where it is used. They may compile but are they correct?

Of course there are things like template specialization to get round these sorts of issue but again it is more knowledge and complexity. Concepts (C++20) can potentially help too but... knowledge and complexity.

One of the reason template functions can be a problem is that they tend to be  global and exibit many of the issues of Global Variables. While they are not setable, as with a variable, the can be accessed and used almost anywhere and once they have been change become a problem.

A way of reducing the scope of  a template issue is to put it in a class


```c++

class Class
{
public:
template<typename S>
S& operator <<(  S& s)
{
    s.write(read());
	return s;
}
std::string read();
};

```

This means the function is now a method that can only be used with the class but is still is able to stream to anything. So if there is a problem it is going to be in the class or in the stream and the scope to be investigated is much smaller. Also, unless you have written your own, streams are generally well tested library code so the problem is going to be in the class and much easier to reason about.

Of note here is the point that templates are at their best in library code. In part this is because the libraries are well designed and tested and therefore can be well understood and also because templates allow libraries to interface with new and unknown types through code generated by the compilier. The STL demostrates how powerfully this can be. Libraries can also hide the complex aspects of generic code from the coder.

Of course having to write and maintain the above code for every class is in itself problematic and the traditional solution is to push it into a base class and use Inheritance.


```c++

class Base
{
public:
template<typename S>
S& operator <<(  S& s)
{
    s.write(read());
	return s;
}
virtual std::string read()=0;
};

class Class: public Base
{
std::string read() override;
};

```

But as noted in the ["first post"](First-Post.html) Inheritance brings its own rules and complexities ( did we miss a virtual destructor in the Base class?). Also when it comes to changing the base class it has similar problems to the global templates where the effects on all the derived classes need to be understood.

So how does namespacing help? The above code can be written as

```c++
namespace class_ns
{

class Class
{
public:
std::string read();
};

template<typename S>
S& operator <<( Class const & t, S& s)
{
    s.write(t.read());
	return s;
}

std::string Class::read()
{
.....

}

} // namespace class_ns

```

Here the function is again scoped to only apply to the Class while retaining flexibility in the stream. Further because the name of the Type is set by the Namespace most of the coded can be replaced with includes and reused for multiple Types.

```c++
namespace class_ns
{

#include "class_declarations.inc"
#include "stream_declarations.inc"

std::string Class::read()
{
.....

}

} // namespace class_ns

```

Now if change is required for a particular Type the first step is to replace the effected include with the code and modify it locally

```c++
namespace class_ns
{

#include "class_declarations.inc"
//#include "stream_declarations.inc"

template<typename S>
S& operator <<( Class const & t, S& s)
{
  if (condition)
    {
      s.write("A static String");
    }
  else
    {
      s.write(t.read());
    }
  return s;
}

std::string Class::read()
{
.....

}

} // namespace class_ns

```

There are no side effects from the changes which can be tested and refined in the working (latest) code without having to modify it. Better yet if the new code would have advantages elsewhere it can move back into the include file to be used by all or another include file to be used as a special case.

```c++
namespace class_ns
{

#include "class_declarations.inc"
#include "stream_special_case_declarations.inc"

std::string Class::read()
{
.....

}

} // namespace class_ns

```


None of the code is complex C++ and the rules are comparatively simple. In fact about the only rule is that all names refer to the scope of the namespace and to refer to the Class outside the namespace just use the namespace class_ns::Class. This overhead can be removed with a using statement.


```c++
using Exposed = class_ns::Class;
```

This approach also encourages Type Safety by allowing even simple classes to be build out of reusable components (include files).

Take for example the classic definition position class

```c++
class Position
{
public:
	Position(int,int);
};
```

The constructor takes two int we know are probably x and y but is it (x,y) or (y,x) when we come to use it?

Replacing the ints with Types means we get a compile error if we remember wrong.

```c++
 class Position
{
public:
	Position(X,Y);
};
```
and the namespace technique allow us to easily define the types

```c++
namespace x_ns
{
#include "axis_value_declarations.inc"
}
using X= x_ns::Class;
namespace y_ns
{
#include "axis_value_declarations.inc"
}
using Y= y_ns::Class;
```

with the include file being something like

```c++
class Class
{
 public:
  Class() = default;
  explicit Class(int val) : value{val} {}
  int value{0};
  bool operator==(Class const& rhs) const { return value == rhs.value; }
....

};
```

Now not only can we not confuse the parameters of the Position constructor but we  get a default value on construction ( trivally 0 here), cannot X=Y or Y=X accidentally, a double or a float cannot be automatically use as a parameter and depending on which operators we choose to implement we can restrict arithmetic functionality ( does multiply make sense?). Similarly we can add other domain specific rules ( if plotting to a screen all values are positive) as we go/discover them along the way.

Of course from the perspective of simplicity and mantenance we may not want to add too many operators and rules. Once we have localized the effects of code using Types and Namespaces it is often easier to return to the primitive domain than  provide operators and overloads to deal with all the requirements. For example

```c++
Position new_position( Position p)
{
  double x = p.x.value;
  double y = p.y.value;

  // do complex floating point calculation here

  return Postion{X{static_cast<int>(x)}, Y{static_cast<int>(y)}};
}
```

While the lack of types may allow for errors in the calculation within the function the scope of any error is confined to the function and there is a cognitive advantage to the coder in thinking in only well known primatives. Provided functions are kept short ( as is good practise) and parameters and return values are always passed as Types many of the potential errors such as transposition are checked regularly.

Further the code, while made up of more characters, can be easier to read and comprehend as what is being done is much more explict. Even the use of static_cast in the above code tells us that x and y are NOT ints in this function and must therefore be thought of differently in this local case from other usages.

This also extends to the compiler. While the extra characters are colapsed away by the compiler so that everything in the end is a primative some of the information provided can help it produce more performant assembler. For example the fact that X and Y are different types means that they cannot occupy the same memory address and it is therefore unecessary to ever check for this condition at the assembly level.

So returning to the issue of the inexperienced coder dealing with a significant codebase. Using Namespaces and Types in this manner allows for simpler code that can be reasoned about locally with fewer hidden rules. The use of include files say to the coder that the code within is boiler plate and that it is the other code that is performing the requirements of the function or class. if templates are used they are simple to comprehend with the generic complexity being left to libraries or hidden in include files. Similarly with Inheritance if a use case arises.

If the boiler plate of the include files is insufficient for a particular requirement the include file can always be replaced and the code within used as a starting point for the unique features. This can be done with no impact on code outside the local scope that is being modified. The inexperience coder can be more adventurous in the code he trys as there are no side effects and for the same reason code review is easier.

