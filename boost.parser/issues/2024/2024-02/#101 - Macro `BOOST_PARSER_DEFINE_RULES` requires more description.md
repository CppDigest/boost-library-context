# #101 - Macro `BOOST_PARSER_DEFINE_RULES` requires more description [Closed]

> Username: akrzemi1  
> Created at: 2024-02-21 21:27:04 UTC  
> Updated at: 2024-03-15 21:29:26 UTC  
> Closed at: 2024-03-15 21:29:26 UTC  
> Url: https://github.com/boostorg/parser/issues/101  

The reference page for `BOOST_PARSER_DEFINE_RULES` is very scarce.  
It should say that you can only call this macro at namespace scope.  
  
Also, I would like to see an example how declaring a rule in a header file and defining it in a cpp file looks like. Can I declare a rule at class scope? If so, at what scope do I invoke the macro?  
  
Can I declare a rule at a namespace scope in a header file? Does it not violate ODR?

---

## Comment 1

> Username: akrzemi1  
> Created at: 2024-02-21 21:29:19 UTC  
> Url: https://github.com/boostorg/parser/issues/101#issuecomment-1957989031  

Also, in the reference page maybe add a reference back to the tutorial on rules.

---

## Comment 2

> Username: tzlaine  
> Created at: 2024-02-24 03:37:14 UTC  
> Url: https://github.com/boostorg/parser/issues/101#issuecomment-1962237402  

Turns out, you cannot actually separately compile rules any more.  You could at some point, but then I made decisions about how context objects work that broke that.  So, part of the commit for this issue removes that claim that rules are separately-compilable.  You can still declare a function `parse_my_stuff(std::string_view stuff)`, and then put its implementation in a .cpp file, but rules themselves rely on too many template parameters that might vary during the parse to be explicitly instantiated.
