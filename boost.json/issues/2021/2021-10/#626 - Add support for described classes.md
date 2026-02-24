# #626 - Add support for described classes [Closed]

> Username: pdimov  
> Created at: 2021-10-02 17:29:21 UTC  
> Updated at: 2022-10-20 19:49:49 UTC  
> Closed at: 2022-10-20 19:49:49 UTC  
> Url: https://github.com/boostorg/json/issues/626  

Basically add https://www.boost.org/doc/libs/1_77_0/libs/describe/doc/html/describe.html#example_to_json and https://www.boost.org/doc/libs/1_77_0/libs/describe/doc/html/describe.html#example_from_json to the library proper so that described structs and classes work automagically, with no additional user effort.  
  
An open issue is how to treat base classes; in the first iteration, we may only enable support for types with no base classes (as reported by `describe_bases`), which will allow us to postpone the decision without breaking code.
