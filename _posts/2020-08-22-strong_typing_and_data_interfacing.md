---
theme: jekyll-theme-hacker
layout: default
title: Strong Typing and Data Interfacing
date: 2020-08-22  00:00:00
---

# Strong Typing and Data Interfacing

This relates back to my first post ["Reducing  Inheritance ( and code) with Type Safety using Namespaces"](First-Post.html) and a follow up [Type Safety and Simplicity](2018-09-10-type_safety_and_simplicity.html).

In the latter I looked at Templates and Generic Code and found them wanting outside the trivial and in Libraries such as the STL. I took this position based in part on the confusion that resulted from the complex error messages, particularly in the case of junior programmers and those inexperienced with the code. 

I attempted to demonstrate that using Namespaces, Strong Typing and Included Code Files could be used to produce more readable and understandable code that could be interperated locally and modified without significant and possibly unknown side effects.

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

The first step is to Forward Declare a Class for the Table and Type for each column of the Schema in an appropriate Namespace. The reason for using Forward Declarations is  to limit the compilation overhead and declaration complexity that results from including headers within headers. By limiting the full declaration of Classes to headers in the Code Files (*.cpp) or Translation Unit the required header for each file is clearer and more stable, not requiring includes that bleed through the header files.

The declaration begins with the Table Class itself and some associated housekeeping classes Id and Vector. Since all Table Classes have an Id with consistent characteristic a separate namespace is not required.

For the remaining columns each get a unique namespace laid out in a tree structure where they have common characteristics for example emergency_ns. The _ns extension to the namespaces is used to avoid conflict with other names and keywords like operator, or class.

For each Type a using statement allows the class in each case to be used without the repetition of the namespace. This can be extended to deeper namespaces provided uniqueness is maintained as shown in the last line.


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
using Personal_details = personal_ns::details_ns::Class;
```

A significant advantage of doing Forward Declarations is that all Type naming can be located in one file or files with a consistent naming structure as per this example
### forward.h
```c++
#include configuration_forward.h
#include data_forward.h
#include logic_forward.h
```
## Header File

Next the Header file. Some irrelevant code is not present but note that the Namespace Structure is repeated as in the Forward file and each class is separately defined as
```c++
      class Class
      {
#include "composite_class_declarations.inc"
      };
```
In this example each Type class declaration simply contains a standardized include file which defines the common charateristics of the class. The reason the class is declared around the include file is that it allows additional class specific methods ( and less common variables) to be added. This is shown later in the declaration  of the Table class.

```c++
#include "field_values.h"
#include "forward.h"

namespace personal_ns::details_ns
{
  namespace name_ns
  {
    namespace name_ns
    {
      class Class
      {
#include "composite_string_class_declarations.inc"
      };
    } // namespace name_ns
    namespace full_ns
    {
      class Class
      {
#include "composite_string_class_declarations.inc"
      };
    } // namespace full_ns
  } // namespace name_ns
  namespace phone_ns
  {
    class Class
    {
#include "composite_string_class_declarations.inc"
    };
  } // namespace phone_ns
  namespace mobile_ns
  {
    class Class
    {
#include "composite_string_class_declarations.inc"
    };
  } // namespace mobile_ns
  namespace email_ns
  {
    class Class
    {
#include "composite_string_class_declarations.inc"
    };
  } // namespace email_ns
  namespace address_ns
  {
    namespace physical_ns
    {
      class Class
      {
#include "composite_string_class_declarations.inc"
      };
    } // namespace physical_ns
    namespace postal_ns
    {
      class Class
      {
#include "composite_string_class_declarations.inc"
      };
    } // namespace postal_ns
  } // namespace address_ns
  namespace partner_ns
  {
    namespace name_ns
    {
      class Class
      {
#include "composite_string_class_declarations.inc"
      };
    } // namespace name_ns
    namespace phone_ns
    {
      class Class
      {
#include "composite_string_class_declarations.inc"
      };
    } // namespace phone_ns
  } // namespace partner_ns
  namespace emergency_ns
  {
    namespace name_ns
    {
      class Class
      {
#include "composite_string_class_declarations.inc"
      };
    } // namespace name_ns
    namespace phone_ns
    {
      class Class
      {
#include "composite_string_class_declarations.inc"
      };
    } // namespace phone_ns
  } // namespace emergency_ns
  namespace status_ns
  {
    class Class
    {
#include "composite_string_class_declarations.inc"
    };
  } // namespace status_ns
  namespace licence_ns
  {
    namespace licences_ns
    {
      class Class
      {
#include "composite_string_class_declarations.inc"
      };
    } // namespace licences_ns
    namespace number_ns
    {
      class Class
      {
#include "composite_string_class_declarations.inc"
      };
    } // namespace number_ns
    namespace version_ns
    {
      class Class
      {
#include "composite_string_class_declarations.inc"
      };
    } // namespace version_ns
  } // namespace licence_ns
  namespace kiwisaver_ns
  {
    class Class
    {
#include "composite_bool_class_declarations.inc"
    };
  } // namespace kiwisaver_ns
  namespace ird_ns
  {
    class Class
    {
#include "composite_string_class_declarations.inc"
    };
  } // namespace ird_ns
  namespace team_ns
  {
    class Class
    {
#include "composite_string_class_declarations.inc"
    };
  } // namespace team_ns
  namespace exemption_ns
  {
    class Class
    {
#include "composite_string_class_declarations.inc"
    };
  } // namespace exemption_ns
  namespace date_ns
  {
    namespace start_ns
    {
      class Class
      {
#include "composite_optional_date_class_declarations.inc"
      };
    } // namespace start_ns
    namespace birth_ns
    {
      class Class
      {
#include "composite_optional_date_class_declarations.inc"
      };
    } // namespace birth_ns
    namespace update_ns
    {
      class Class
      {
#include "composite_optional_date_class_declarations.inc"
      };
    } // namespace update_ns
  } // namespace date_ns

  namespace callbacks
  {
#include "composite_callbacks_change_declarations.inc"
  } // namespace callbacks
  class Id
  {
#include "composite_id_declarations.inc"
  };
#include "composite_alt_id_declarations.inc"
  // Field Structure
  // Field{"name",is_insert,read_only,is_required,"default_value"},

  using Fields = std::tuple<Field<Alt_id>, Field<Id>, Field<name_ns::Name>, Field<Mobile>, Field<name_ns::Full>, Field<Phone>, Field<Email>,
                           Field<address_ns::Physical>, Field<address_ns::Postal>, Field<partner_ns::Name>,
                           Field<partner_ns::Phone>, Field<emergency_ns::Name>, Field<emergency_ns::Phone>, Field<Status>,
                           Field<licence_ns::Licences>, Field<date_ns::Start>, Field<Kiwisaver>, Field<IRD>, Field<licence_ns::Number>,
                           Field<licence_ns::Version>, Field<date_ns::Birth>, Field<Team>, Field<Exemption>, Field<date_ns::Update>>;
  static constexpr Fields fields{
      // TABLE PersonalDetails 						-- ACTIONS: UPDATE ONLY
      Field<Alt_id>{"alt_id", false, true, false, ""},
      //	operID int NOT NULL,					-- The record ID from server
      Field<Id>{"operID", false, true, true, ""},
      //	operName varchar(250) NOT NULL,			-- RO		The operators mobile
      // login name
      Field<name_ns::Name>{"operName", true, true, true, ""},
      //	operMobile varchar(50) NULL,			-- UPD 		Operators mobile
      // number
      Field<Mobile>{"operMobile", false, false, false, ""},
      //	operFullName varchar(255) NULL,			-- UPD 		Full name
      Field<name_ns::Full>{"operFullName", false, false, false, ""},
      //	operPhone varchar(20) NULL,				-- UPD Home phone number
      Field<Phone>{"operPhone", false, false, false, ""},
      //	operEmail varchar(100) NULL,			-- UPD 		Personal email
      // address
      Field<Email>{"operEmail", false, false, false, ""},
      //	operAddress varchar(255) NULL,			-- UPD 		Home address
      Field<address_ns::Physical>{"operAddress", false, false, false, ""},
      //	operPostal varchar(255) NULL,			-- UPD 		Postal address
      Field<address_ns::Postal>{"operPostal", false, false, false, ""},
      //	operPartnerName varchar(100) NULL,		-- UPD 		Partners name (or
      // next of Kin)
      Field<partner_ns::Name>{"operPartnerName", false, false, false, ""},
      //	operPartnerPhone varchar(50) NULL,		-- UPD 		Partners phone
      Field<partner_ns::Phone>{"operPartnerPhone", false, false, false, ""},
      //	operEmergencyName varchar(100) NULL,	-- UPD 		Emergency contact person
      Field<emergency_ns::Name>{"operEmergencyName", false, false, false, ""},
      //	operEmergencyPhone varchar(50) NULL,	-- UPD 		Emergency contact number/s
      Field<emergency_ns::Phone>{"operEmergencyPhone", false, false, false, ""},
      //	operStatus varchar(10) NOT NULL,		-- RO 		Employment status
      Field<Status>{"operStatus", false, true, true, ""},
      //	operLicenses varchar(50) NULL,			-- RO 		Driver licence
      // classes
      Field<licence_ns::Licences>{"operLicenses", false, true, false, ""},
      //	operStartDate varchar(10) NULL,			-- RO 		Date started work
      Field<date_ns::Start>{"operStartDate", false, true, false, ""},
      //	operKiwisaver bit NULL,					-- RO Belongs to Kiwisaver
      Field<Kiwisaver>{"operKiwisaver", false, true, false, ""},
      //	operIRDNumber varchar(20) NULL,			-- RO 		IRD number
      Field<IRD>{"operIRDNumber", false, true, false, ""},
      //	operLicenceNumber varchar(30) NULL,		-- RO 		Driver licence
      // number
      Field<licence_ns::Number>{"operLicenceNumber", false, true, false, ""},
      //	operLicenceVers varchar(30) NULL,		-- RO 		Licence version
      // number
      Field<licence_ns::Version>{"operLicenceVers", false, true, false, ""},
      //	operBirthDate varchar(10) NULL,			-- RO 		DOB
      Field<date_ns::Birth>{"operBirthDate", false, true, false, ""},
      //	Team varchar(250) NOT NULL,				-- RO 		GAVINS team
      Field<Team>{"Team", false, true, false, ""},
      //	Exemption varchar(250) NOT NULL			-- RO 		Any current truck
      // driver exemption
      Field<Exemption>{"Exemption", false, true, false, ""}, 
	  //
	  Field<date_ns::Update>{"Update", false, true, false, ""}
      //
  };
  using Tuple = std::tuple<Alt_id, Id, name_ns::Name, Mobile, name_ns::Full, Phone, Email,
                           address_ns::Physical, address_ns::Postal, partner_ns::Name,
                           partner_ns::Phone, emergency_ns::Name, emergency_ns::Phone, Status,
                           licence_ns::Licences, date_ns::Start, Kiwisaver, IRD, licence_ns::Number,
                           licence_ns::Version, date_ns::Birth, Team, Exemption, date_ns::Update>;
  static_assert(std::tuple_size<Tuple>::value == 24);
  static_assert(std::tuple_size<Tuple>::value == std::tuple_size<Fields>::value);
  class Class
  {
#include "composite_class_declarations.inc"
    Team team() const;
  };
// --> free functions
#include "composite_free_function_declarations.inc"

  Class registed_user_details();
  // <-- free functions
} // namespace gavops_ns::personal_ns::details_ns

```
### Field Structure

After the Column Types have been declared a Field (column) structure is defined in the form of a Tuple called Fields.
```c++
  // Field Structure
  // Field{"name",is_insert,read_only,is_required,"default_value"},

  using Fields = std::tuple<Field<Alt_id>, Field<Id>, Field<name_ns::Name>, Field<Mobile>, Field<name_ns::Full>, Field<Phone>, Field<Email>,
                           Field<address_ns::Physical>, Field<address_ns::Postal>, Field<partner_ns::Name>,
                           Field<partner_ns::Phone>, Field<emergency_ns::Name>, Field<emergency_ns::Phone>, Field<Status>,
                           Field<licence_ns::Licences>, Field<date_ns::Start>, Field<Kiwisaver>, Field<IRD>, Field<licence_ns::Number>,
                           Field<licence_ns::Version>, Field<date_ns::Birth>, Field<Team>, Field<Exemption>, Field<date_ns::Update>>;
  static constexpr Fields fields{
      // TABLE PersonalDetails 						-- ACTIONS: UPDATE ONLY
      Field<Alt_id>{"alt_id", false, true, false, ""},
      //	operID int NOT NULL,					-- The record ID from server
      Field<Id>{"operID", false, true, true, ""},
      //	operName varchar(250) NOT NULL,			-- RO		The operators mobile
      // login name
      Field<name_ns::Name>{"operName", true, true, true, ""},
      //	operMobile varchar(50) NULL,			-- UPD 		Operators mobile
      // number
      Field<Mobile>{"operMobile", false, false, false, ""},
//...
```

The Tuple consists of a Field for each column in the Table with each Field templated to the Type of a column. Note that in this real world case an additional Id (alt_id) was required so there are columns+1 Fields. This is not part of the discussion but has the interesting side effect that the columns start numbering at 1 instead of 0;

Field is a simple structure Template defined in field_values.h

```c++
template<typename T>
struct Field
{
  std::string_view column_name;
  bool is_insert;
  bool read_only;
  bool is_required;
  std::string_view default_value;
};
```

The values in Field simply reflect the characteristics of the column necessary for it to interact with the underlying SQL tables.

Note that the order of the Fields is technically not important but in this real world case MUST match the order of the Columns. This is due to the way the persistent storage of data (files) works in this example where order must be maintained.

A static constexpr instance of Fields is then defined (fields) and a Field instance created for each column. Note that as this is the only place that a Field is defined this construction is done at compile time (constexpr) and std::string_view can be used to connect to the string literals.

Note also that the intermingling of the instance definitions and the comments ( the original schema) allows a direct association between the Column Descriptions and Field Definitions. Historically this has been a common error source since the schema itself has no real meaning in code. The close coupling help to ensure that spelling etc. can be easily checked and updated.

### Data Tuple

A Data Tuple (Tuple) is now defined. This is used to contain the actual Table and Column data, a Record in effect. It simply has template parameter that agree with the Columns in the Table. Order is not important s the Tuple and Field are bound based on type.

```c++
  using Tuple = std::tuple<Alt_id, Id, name_ns::Name, Mobile, name_ns::Full, Phone, Email,
                           address_ns::Physical, address_ns::Postal, partner_ns::Name,
                           partner_ns::Phone, emergency_ns::Name, emergency_ns::Phone, Status,
                           licence_ns::Licences, date_ns::Start, Kiwisaver, IRD, licence_ns::Number,
                           licence_ns::Version, date_ns::Birth, Team, Exemption, date_ns::Update>;
  static_assert(std::tuple_size<Tuple>::value == 24);
  static_assert(std::tuple_size<Tuple>::value == std::tuple_size<Fields>::value);
```

At this point it may be questioned as to why two Tuples have been defined instead of a single Tuple for Field Characteristics and Data. The reason is Foreign Key columns. This will be discussed later but while this example has no Foreign Keys, when they exist the have different Field Characteristics in different Namespaces (Tables). Allowing different characteristics in different namespaces allows the same Type to be used in all instances which in turn allows Type features like equality to be maintained.

Also of note are the static_asserts. These check that the correct number of types are in the Data Tuple and that a matching number of Fields are defined. This is another historically common error source particularly when modifying a Table Schema. If either of these assertions fail the code will not compile.

Compilation will also fail if there is a mismatch in the types of the Fields and the Data Tuple ensuring that neither can become unsynchronized.

### Table Class and Free Function Declarations

The Table Class is declared much the same way as the Column Types. Here we can see a specialized Class Method being declared to extend the class beyond it standardized common characteristics.

Standardized Free Functions are also declared along with sepecialized Functions. Note that this standardization is only possible due to Namespacing. This allows the functions to be defined in terms of generic names such as Class and Id and therefore be texturally equivalent. This is an alternative method of generic programming that avoid some of the issues of Templates like long and complicated error messages. However, although errors can be short and clear, their location can be differcult to find as compilers and IDEs do not understand the include file structure. There are meta techniques that can be used to mitigate some of this but they are beyond the scope of the current discussion.

```c++
  class Class
  {
#include "composite_class_declarations.inc"
    Team team() const;
  };
// --> free functions
#include "composite_free_function_declarations.inc"

  Class registed_user_details();
  // <-- free functions
```

## Code File

The Code File is again shown largely in full but, as can be expected, has many features not relevant to the discussion and those details will be ignored.

The Column Types are defined in much that same way as they were declared in the  Header file using standardized include Definition files. Note that this significaly reduces the line count of the Code File while maintaining basic discriptive information in the include file names.

Otherwise the most significant point of interest is the Class Method and how it is used to access data.

```c++
#include "personal_details.h"

namespace gavops_ns::personal_ns::details_ns
{
  namespace // internal linkage
  {
  } // namespace
  namespace name_ns
  {
    namespace name_ns
    {
#include "composite_string_class_definitions.inc"
    } // namespace name_ns
    namespace full_ns
    {
#include "composite_string_class_definitions.inc"
    } // namespace full_ns
  }   // namespace name_ns
  namespace phone_ns
  {
#include "composite_string_class_definitions.inc"
  } // namespace phone_ns
  namespace mobile_ns
  {
#include "composite_string_class_definitions.inc"
  } // namespace mobile_ns
  namespace email_ns
  {
#include "composite_string_class_definitions.inc"
  } // namespace email_ns
  namespace address_ns
  {
    namespace physical_ns
    {
#include "composite_string_class_definitions.inc"
    } // namespace physical_ns
    namespace postal_ns
    {
#include "composite_string_class_definitions.inc"
    } // namespace postal_ns
  }   // namespace address_ns
  namespace partner_ns
  {
    namespace name_ns
    {
#include "composite_string_class_definitions.inc"
    } // namespace name_ns
    namespace phone_ns
    {
#include "composite_string_class_definitions.inc"
    } // namespace phone_ns
  }   // namespace partner_ns
  namespace emergency_ns
  {
    namespace name_ns
    {
#include "composite_string_class_definitions.inc"
    } // namespace name_ns
    namespace phone_ns
    {
#include "composite_string_class_definitions.inc"
    } // namespace phone_ns
  }   // namespace emergency_ns
  namespace status_ns
  {
#include "composite_string_class_definitions.inc"
  } // namespace status_ns
  namespace licence_ns
  {
    namespace licences_ns
    {
#include "composite_string_class_definitions.inc"
    } // namespace licences_ns
    namespace number_ns
    {
#include "composite_string_class_definitions.inc"
    } // namespace number_ns
    namespace version_ns
    {
#include "composite_string_class_definitions.inc"
    } // namespace version_ns
  }   // namespace licence_ns
  namespace kiwisaver_ns
  {
#include "composite_bool_class_definitions.inc"
  } // namespace kiwisaver_ns
  namespace ird_ns
  {
#include "composite_string_class_definitions.inc"
  } // namespace ird_ns
  namespace team_ns
  {
#include "composite_string_class_definitions.inc"
  } // namespace team_ns
  namespace exemption_ns
  {
#include "composite_string_class_definitions.inc"
  } // namespace exemption_ns
  namespace date_ns
  {
    namespace start_ns
    {
#include "composite_optional_date_class_definitions.inc"
    } // namespace start_ns
    namespace birth_ns
    {
#include "composite_optional_date_class_definitions.inc"
    } // namespace birth_ns
    namespace update_ns
    {
#include "composite_optional_date_class_definitions.inc"
    } // namespace update_ns
  }   // namespace date_ns

  namespace callbacks
  {
#include "composite_callbacks_change_definitions.inc"
  } // namespace callbacks
#include "composite_insert_names_definitions.inc"
  namespace persistent
  {
#include "persistent_set_clause_templates.inc"
#include "persistent_struct_definition.inc"
    namespace // internal linkage
    {
      table::Table const &table_values() { return table::personal::details::table_values; }
    } // namespace
#include "persistent_dec_defs.inc"
  } // namespace persistent
#include "composite_id_definitions.inc"
#include "persistent_set_clause_data_templates.inc"
  class Class::Data
  {
#include "persistent_class_data.inc"
  };

#include "composite_class_definitions.inc"
  Team Class::team() const { return get<Team>(); }

#include "composite_free_function_definitions.inc"
  Class registed_user_details()
  {
    auto id = configure::registered_user_value();
    Class instance;
    instance.data().dp(persistent::map()[id]);
    instance.load_from_dp();
    return instance;
  }
  // <-- free functions
} // namespace personal_ns::details_ns
```

## Accessing Data

Accessing Data is possible in a number of ways due the use of Tuples and Unique Types.

In the Class Method above data is accessed in a traditional way by using a function to read ( and a function to write) to each data field ( getters and setters).
```c++
	auto value = instance.method();
	instance.method(value);
```
This however requires at least one function per column to read, in this example 23 functions, and one per column to write where required ( potentially another 23) that are largely repedative boilerplate.

In the definition of the above Team Method
```c++
  Team Class::team() const { return get<Team>(); }
```
it can be seen that it is expressed in terms of a get Template function and this is an alternative way to access data.

```c++
	auto value = instance.get<Type>();
```
Because each Type in the Class/Data Tuple is unique the correct value required can be indentified by the Type alone. Thus the need for boilerplate Class Methods is eliminated at the expense of possibly more characters type at call time. However the additional typing is offset by the clarity of the actual Type being used.

Similarly there is an equivalent set functions with additional typing.
```c++
	auto value;
    instance.set<Type>(value);
```
However in this case if the value is itself Strongly Typed typing is more often reduced.
```c++
	Type value;
    instance.set(value);
```
Further  a get function is possible

```c++
	Type value = instance.get(value);
```
Here the additional function parameter is used solely to determine the Type and this is equally possible
```c++
	Type type_example;
	Type value = instance.get(type_example);
```

A significant benefit of using the get/set functions is that it encourages the use on Strong Typing in other parts of the code resulting in a style where primitive and ambiguous types ( int, double, std::string) are used for calculation and/or efficiency reasons only and are converted to strong types as soon as possible. This helps avoid the clasical issues of mis-ordered or mis-understood variables being used.

Note that this does not mean that getters and setters will never be written. In the example above the Team method was written because some of the locations it was called required the full Namespace to be written each time. Using the Class Method Argument Dependent Lookup (ADL) allows the correct function to be located based on the class. This need is however mitigated by the following.

It is also possible to write both the following

```c++
	Type value = instance;
...
    instance=value;
```
This is because the instance (Class) has only one Data value of the Type so the get/set methods required can be automatically determined via ADL. This can significantly reduce both the typing (keystrokes) required and the number of specific functions to be remembered. Once the value it typed no further specialized functions are required to read or write data.


Finally this feature can be extended to other operators
```c++
	Type value;
	value == instance;
    instance < value;
    instance += value;
```
Note that the get/set and operator functions are all implemented using Template defined in the Include Files and can thus be made to "just work" without any addional and/or confusing code being written by the programmer.

## Conclusion

I hope I have shown that by using Strong Typing, Code Include Files and judisious use of Templates code can be written that is clearer to read and understand with significant reductions in the required typing (keystrokes)  and overall number of (visible) lines. While I have not explained in detail how this is achieved ( a later post perhaps) the resulting code should be easier to maintain with specialization being clearly expressed as differences from the standardized Include Files.

Further the Forward, Header and Code file structure has significant advantages in localizing coding details into specific locations making tracing of Declarations and Definitions easier.

Finally, although it may be possible to similar genetic programming using Templates alone at a minimum it would requires much more of the code to live in the Header files with the consequencial longer compile times and risks of naming conflict. ( The only way I have thought that this could be done is by replacing all the namespaces with classes that are then used in the Templates to determine the Type structure. However even if this is possible it would require the exposure of many more lines of code and almost inevitable the use of Inheritance, Virtual Functions and Template specializations all of which make the code harder to understand, debug, and localize.)
