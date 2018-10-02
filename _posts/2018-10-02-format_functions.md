---
theme: jekyll-theme-hacker
layout: default
title: Format Functions
date: 2018-10-02  00:00:00
---

# Format Functions

Was working with some date and time code today and needed to generate strings in various formats and realized that namespace help again.

Lets say we have three time classes.

```c++
class Date; // A Calendar Date
class Time; // Time since midnight
class Date_time; // A combination (time_point);
```
and then we have functions that return a formatted time string.

```c++
std::string hours_minutes( Time); // returns "hh:mm"
std::string hours_minutes( Date_Time); // returns "hh:mm" of Time portion
```

Where the Date_Time version has a definition something like

```c++
std::string hours_minutes( Date_Time date_time)
{
return hours_minutes(get_time(date_time));
}
```

Then we want am/pm format.

```c++
std::string hours_minutes_ampm( Time); // returns "hh:mm am/pm"
std::string hours_minutes_ampm( Date_Time); // returns "hh:mm am/pm" of Time portion
```

And then we want another format with spaces and letter format.

```c++
std::string hours_minutes_spaced( Time); // returns "hhH mmM"
std::string hours_minutes_spaced( Date_Time); // returns "hhH mmM" of Time portion
```

So the pattern here is that each format we have to create two new functions with a different name each time.

But lets try this with namespaces. First put the classes in a namespace along with the default functions.

```c++
namespace time
{
class Date; // A Calendar Date
class Time; // Time since midnight
class Date_time; // A combination (time_point);

std::string hours_minutes( Time); // returns "hh:mm"
std::string hours_minutes( Date_Time); // returns "hh:mm" of Time portion
}

```

To declare the am/pm functions we add a namespace and copy in the functions unchanged.

```c++
namespace time
{
class Date; // A Calendar Date
class Time; // Time since midnight
class Date_time; // A combination (time_point);

std::string hours_minutes( Time); // returns "hh:mm"
std::string hours_minutes( Date_Time); // returns "hh:mm" of Time portion

namespace ampm
{
std::string hours_minutes( Time); // returns "hh:mm am/pm"
std::string hours_minutes( Date_Time); // returns "hh:mm am/pm" of Time portion
}
}

```

Now we have obviously to write a new definition for the Time version of the function but the Date_Time version definition is exactly the same as it just calles the Time version in the same namespace.

Similarly we can add the spaced versions by adding the namespace, copying and editing one definition

```c++
namespace time
{
class Date; // A Calendar Date
class Time; // Time since midnight
class Date_time; // A combination (time_point);

std::string hours_minutes( Time); // returns "hh:mm"
std::string hours_minutes( Date_Time); // returns "hh:mm" of Time portion

namespace ampm
{
std::string hours_minutes( Time); // returns "hh:mm am/pm"
std::string hours_minutes( Date_Time); // returns "hh:mm am/pm" of Time portion
}
namespace spaced
{
std::string hours_minutes( Time); // returns "hhH mmM"
std::string hours_minutes( Date_Time); // returns "hhH mmM" of Time portion
}
}

```

Obviously we want to use these function in other code and when we do we just use the namespace to specify which
```c++
auto string = time::hours_minutes(time); // the default format
auto string = time::ampm::hours_minutes(time); // the am/pm format
auto string = time::spaced::hours_minutes(time); // the spaced format
```

Note how we can initially write the first version when developing code and then come back later to add a namespace for a specific format.

Now suppose we want capitalized AM/PM as another format. We can just add/copy/define as before.

```c++
namespace time
{
class Date; // A Calendar Date
class Time; // Time since midnight
class Date_time; // A combination (time_point);

std::string hours_minutes( Time); // returns "hh:mm"
std::string hours_minutes( Date_Time); // returns "hh:mm" of Time portion

namespace ampm
{
std::string hours_minutes( Time); // returns "hh:mm am/pm"
std::string hours_minutes( Date_Time); // returns "hh:mm am/pm" of Time portion

namespace capital
{
std::string hours_minutes( Time); // returns "hh:mm AM/PM"
std::string hours_minutes( Date_Time); // returns "hh:mm AM/PM" of Time portion
}
}
namespace spaced
{
std::string hours_minutes( Time); // returns "hhH mmM"
std::string hours_minutes( Date_Time); // returns "hhH mmM" of Time portion
}
}

```

And if we use some form of intelisense, as we type, we are offered more details versions of the formatting as we go.

The takeaways here is that using namespaces to name repeated versions of functions both introduces a disipline to the naming of the functions and, as with other uses of namespace, also allows repeated code to be simply reused. Yes there are duplicate (Date_Time) functions required in each namespace but this is not any different from what similar generic template code does under the covers.

Templates just generate an instance of a function for the  different parameter requirements. Here we are just doing it explicitly and then letting the linker remove the versions that are not used. The size of the code is similar and without the header/compilation overhead of the templates. ( This is someting I think I need to explore in another post. Note to self can you constexpr template?)

Finally there is the question of whether this sort of code structure is even possible with templates.

Sure you can have a template that handles both the the Time and Date_Time classes

```c++
template< typename T>
std::string hours_minutes( T time); 
```

But which string format is returned? Do you have to add another parameter? 
```c++
template< typename T, typename Type >
std::string hours_minutes( T time, Type type); 
```
And you still have to write a template specialization for each of the different formats so the saving would only be in the Date_Time functions at the cost of having to create Type classes and putting everything in the header.

And since these functions are tools to be used in many code files the compilation overhead is repeated each time.

So even if a template solution could be written any reduction in coding effort would be minimal while the loss of clarity could be significant.

IMO namespaces for the win again.

P.S How would the templated version work with intelisense? Don't use it myself but would the template version of the code give any suggestions for the second (Type) parameter as the namespace version does? Hmm namespacing makes intelisense more useful... who would have thought.

