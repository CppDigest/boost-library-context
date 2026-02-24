# #72 - Parsers don't have names but diagnostic messages [Closed]

> Username: akrzemi1  
> Created at: 2024-01-18 18:47:54 UTC  
> Updated at: 2024-01-20 03:15:09 UTC  
> Closed at: 2024-01-20 03:15:09 UTC  
> Url: https://github.com/boostorg/parser/issues/72  

[Creating a named parser](https://tzlaine.github.io/parser/doc/html/boost_parser__proposed_/tutorial/more_about_rules.html#boost_parser__proposed_.tutorial.more_about_rules.creating_a_named_parser) suggests that the parameter passed to a rule in constructor is not a rule name but rather a portion of a diagnostic message.  
  
Using a converting constructor is tricky.   
  
Did you consider using a default "name" (or diagnostic message), where a use case is not to generate better debugging experience? Boost.PFR already does tricks like converting template parameter names into strings: https://www.boost.org/doc/libs/1_84_0/doc/html/boost_pfr/tutorial.html#boost_pfr.tutorial.reflection_of_field_name

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-01-18 23:28:39 UTC  
> Url: https://github.com/boostorg/parser/issues/72#issuecomment-1899380029  

You can name your rule whatever ou like.  If you care about the rule's name being used to generate end-user-readable messages, I think you should use the diagnastic-as-name convention.  No is going to make you though.  
  
Regardless, I don't exactly understand what the ask is here.  Are you asking for each rule to have a default name?  What would it be, just the name of the rule variable, the name of its type, ...?

---

## Comment 2

> Username: akrzemi1  
> Created at: 2024-01-19 03:56:52 UTC  
> Url: https://github.com/boostorg/parser/issues/72#issuecomment-1899656439  

First, I am making an assumption, and I would like you to verify it. Is it true that the only goal of the rule name it for it to be used in displaying these error messages?  
  
If my assumption is true, then I have two requests:  
  
One. Change the docs a bit so that they call this parameter "description" or "diagnostic description" rather than "name". Because "name" suggests a single noun, whereas "diagnostc description" suggests more that I have to create a gramatical construct that makes the full error message a gramatically correct sentence. An from the documentation I gather that the latter is your intention.  
  
Two. When not provided explicitly, generate a "diagnostic description" of a rule from the name of its tag. This is to cover the case where I do not want to pay attention to error messages, and in that case I shouldn't be forced to mechanically copy the name of the rule.  
  
The third suggestion I had was to make the constructor of `rule` explicit. Converting constructors imply conversions: that you can pass a `U` wherever a `T` is expected:  
  
```c++  
void play_with(bp::rule<A, B> r, std::string input);  
play_with("input talking about bee hive", "bee"); // bug: bad order of arguments  
```  
  
And this is not the case for `rule`: a description is only a secondary property of the rule.

---

## Comment 3

> Username: tzlaine  
> Created at: 2024-01-20 02:32:51 UTC  
> Url: https://github.com/boostorg/parser/issues/72#issuecomment-1901634821  

> First, I am making an assumption, and I would like you to verify it. Is it true that the only goal of the rule name it for it to be used in displaying these error messages?  
>   
> If my assumption is true, then I have two requests:  
  
Ok, thanks for explaining.  
  
> One. Change the docs a bit so that they call this parameter "description" or "diagnostic description" rather than "name". Because "name" suggests a single noun, whereas "diagnostc description" suggests more that I have to create a gramatical construct that makes the full error message a gramatically correct sentence. An from the documentation I gather that the latter is your intention.  
  
Sure, that seems like a good change to me.  
  
> Two. When not provided explicitly, generate a "diagnostic description" of a rule from the name of its tag. This is to cover the case where I do not want to pay attention to error messages, and in that case I shouldn't be forced to mechanically copy the name of the rule.  
  
I don't think this one is very attractive.  The user should just provide a name.  It's not a great burden.  
  
> The third suggestion I had was to make the constructor of `rule` explicit. Converting constructors imply conversions: that you can pass a `U` wherever a `T` is expected:  
>   
> ```c++  
> void play_with(bp::rule<A, B> r, std::string input);  
> play_with("input talking about bee hive", "bee"); // bug: bad order of arguments  
> ```  
>   
> And this is not the case for `rule`: a description is only a secondary property of the rule.  
  
This is true in the general case, of course.  It doesn't really apply to `rule` though.  The only value of a `rule` is as a type.  You would never pass it as a function parameter unless you wanted a copy of that rule, with a different name -- since name is its only data.  So I think that function is fine.  Where the confusion would come in would be if you had two functions, one of which took a rule and one of which took a `char const *`.  You might call the wrong one by mistake.  That would be sad.  However, no one is passing `rule`s around in function parameters, or at least never that I've seen.  I've seen a lot of Spirit code.
