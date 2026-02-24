# #55 - Underspecified semantics of functions and io [Closed]

> Username: apolukhin  
> Created at: 2020-10-14 15:20:14 UTC  
> Updated at: 2020-10-15 19:12:40 UTC  
> Closed at: 2020-10-15 19:12:40 UTC  
> Url: https://github.com/boostorg/pfr/issues/55  

* what are the requirements on fields  
* Common io questions: What format of the serialized output does the library guarantee? Does it guarantee any format at all? Do you leave yourself a freedom to change it in the future without notice? Can users customize it? If so, where is it specified?  
* refer to std:: alternatives (like std::tie in structure_tie)  
* "provides other std::tuple like methods for user defined types without any macro or boilerplate code." and BOOST_PFR_FUNCTIONS_FOR contradictions
