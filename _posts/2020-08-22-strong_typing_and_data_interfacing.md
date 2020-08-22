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

The first step is to Forward Declare a Class for the Table and Type for each column of the Schema in an appropriate Namespace. The reason for using Forward Declaration is limit the compilation overhead and declaration complexity that results from including headers within headers. By limiting the full declaration of Classes to headers in the Translation Unit Files (*.cpp) the required header for each Unit is clearer and more stable, not requiring includes that bleed through the header files.

The declaration begins with the Table Class itself and some associated housekeeping classes Id and Vector. Since all Table Classes have an Id with consistent characteristic a separate namespace is not required.

For the remaining columns each get a unique namespace laied out in a tree structure where they have common characteristics for example emergency_ns. The _ns extension to the namespaces is used to avoid conflict with other names and keywords like operator, or class.

For each Type a using statement allows the class in each case to be used without the repetition of the namespace. This could be extended of course to deeper namespaces provided uniqueness is maintained.


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

A significant advantage of doing Forward Declarations is that all Type naming can be located in one file or files with a consistent naming structure
### forward.h
```c++
#include configuration_forward.h
#include data_forward.h
#include logic_forward.h
```
## Header File

Next the Header file. Some irrelevant code is not present but note that the Namespace Structure is repeated and each class is separately defined as
```c++
      class Class
      {
#include "composite_class_declarations.inc"
      };
```
In this example each class declaration simply contains a standardized include file which defines the common charateristics of the class. The reason the class is declared around the include file is that it allows additional class specific methods ( and less common variables) to be added. The details of the added methods and the include files will be discussed later.

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

  using Fields = std::tuple<Field, Field, Field, Field, Field, Field, Field, Field, Field, Field,
                            Field, Field, Field, Field, Field, Field, Field, Field, Field, Field,
                            Field, Field, Field, Field>;
  static constexpr Fields fields{
      // TABLE PersonalDetails 						-- ACTIONS: UPDATE ONLY
      Field{"alt_id", false, true, true, ""},
      //	operID int NOT NULL,					-- The record ID from server
      Field{"operID", false, true, true, ""},
      //	operName varchar(250) NOT NULL,			-- RO		The operators mobile
      // login name
      Field{"operName", true, true, true, ""},
      //	operMobile varchar(50) NULL,			-- UPD 		Operators mobile
      // number
      Field{"operMobile", false, false, false, ""},
      //	operFullName varchar(255) NULL,			-- UPD 		Full name
      Field{"operFullName", false, false, false, ""},
      //	operPhone varchar(20) NULL,				-- UPD Home phone number
      Field{"operPhone", false, false, false, ""},
      //	operEmail varchar(100) NULL,			-- UPD 		Personal email
      // address
      Field{"operEmail", false, false, false, ""},
      //	operAddress varchar(255) NULL,			-- UPD 		Home address
      Field{"operAddress", false, false, false, ""},
      //	operPostal varchar(255) NULL,			-- UPD 		Postal address
      Field{"operPostal", false, false, false, ""},
      //	operPartnerName varchar(100) NULL,		-- UPD 		Partners name (or
      // next of Kin)
      Field{"operPartnerName", false, false, false, ""},
      //	operPartnerPhone varchar(50) NULL,		-- UPD 		Partners phone
      Field{"operPartnerPhone", false, false, false, ""},
      //	operEmergencyName varchar(100) NULL,	-- UPD 		Emergency contact person
      Field{"operEmergencyName", false, false, false, ""},
      //	operEmergencyPhone varchar(50) NULL,	-- UPD 		Emergency contact number/s
      Field{"operEmergencyPhone", false, false, false, ""},
      //	operStatus varchar(10) NOT NULL,		-- RO 		Employment status
      Field{"operStatus", false, true, true, ""},
      //	operLicenses varchar(50) NULL,			-- RO 		Driver licence
      // classes
      Field{"operLicenses", false, true, false, ""},
      //	operStartDate varchar(10) NULL,			-- RO 		Date started work
      Field{"operStartDate", false, true, false, ""},
      //	operKiwisaver bit NULL,					-- RO Belongs to Kiwisaver
      Field{"operKiwisaver", false, true, false, ""},
      //	operIRDNumber varchar(20) NULL,			-- RO 		IRD number
      Field{"operIRDNumber", false, true, false, ""},
      //	operLicenceNumber varchar(30) NULL,		-- RO 		Driver licence
      // number
      Field{"operLicenceNumber", false, true, false, ""},
      //	operLicenceVers varchar(30) NULL,		-- RO 		Licence version
      // number
      Field{"operLicenceVers", false, true, false, ""},
      //	operBirthDate varchar(10) NULL,			-- RO 		DOB
      Field{"operBirthDate", false, true, false, ""},
      //	Team varchar(250) NOT NULL,				-- RO 		GAVINS team
      Field{"Team", false, true, false, ""},
      //	Exemption varchar(250) NOT NULL			-- RO 		Any current truck
      // driver exemption
      Field{"Exemption", false, true, false, ""}, Field{"Update", false, true, false, ""}
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
#include "composite_test_data_declarations.inc"
} // namespace gavops_ns::personal_ns::details_ns

```
### Field Structure

After the Column Types have been declared a Field (column) structure is defined in the form of a Tuple called Fields.
```c++
  // Field Structure
  // Field{"name",is_insert,read_only,is_required,"default_value"},

  using Fields = std::tuple<Field, Field, Field, Field, Field, Field, Field, Field, Field, Field,
                            Field, Field, Field, Field, Field, Field, Field, Field, Field, Field,
                            Field, Field, Field, Field>;
  static constexpr Fields fields{
      // TABLE PersonalDetails 						-- ACTIONS: UPDATE ONLY
      Field{"alt_id", false, true, true, ""},
      //	operID int NOT NULL,					-- The record ID from server
      Field{"operID", false, true, true, ""},
      //	operName varchar(250) NOT NULL,			-- RO		The operators mobile
      // login name
      Field{"operName", true, true, true, ""},
      //	operMobile varchar(50) NULL,			-- UPD 		Operators mobile
      // number
      Field{"operMobile", false, false, false, ""},
//...
```

The Tuple consists of a Field for each column in the Table. Note that in this real world case an additional Id (alt_id) was required so there are columns+1 Fields. This is not part of the discussion but has the interesting side effect that the columns start numbering at 1 instead of 0;

Field is a simple structure defined in field_values.h

```c++
struct Field
{
  std::string_view column_name;
  bool is_insert;
  bool read_only;
  bool is_required;
  std::string_view default_value;
};
```

The values in Field siomply reflect the characteristics of the column necessary for it to interact with the underlying SQL tables.

A static constexpr instance of Fields is then defined (fields) and a Field instance created for each column. Note that as this is the only place that a Field is defined this construction is done at compile time (constexpr) and std::string_view can be used to connect to the string literals.

Note also that the intermingling of the instance definitions and the comments ( the original schema) allows a direct association between the Column Descriptions and Field Definitions. Historically this has been a common error source since the schema itself has no real meaning in code. The close coupling help to ensure that spelling etc. can be easily checked and updated.


