# #51 - Confused with the expected behavior regarding the capitalization of “ß” [Closed]

> Username: olivier-grech  
> Created at: 2020-01-10 13:30:35 UTC  
> Updated at: 2023-03-21 10:18:40 UTC  
> Closed at: 2023-03-06 05:51:09 UTC  
> Url: https://github.com/boostorg/locale/issues/51  

This example (https://www.boost.org/doc/libs/1_71_0/libs/locale/doc/html/conversions.html) indicates that the correct case for the word "grüßen" is as follow:  
  
```  
Upper GRÜSSEN  
Lower grüßen  
Title Grüßen  
Fold  grüssen  
```  
  
However, the wording used in the example `examples/conversions.cpp` seems to indicate that replacing ß by SS is incorrect when using uppercase :  
  
```cpp  
cout<<"For example:"<<endl;  
cout<<"   German grüßen correctly converted to "<<to_upper("grüßen")<<", instead of incorrect "  
                <<boost::to_upper_copy(std::string("grüßen"))<<endl;  
cout<<"     where ß is replaced with SS"<<endl;  
```  
  
I tried running this example (note that I used Boost 1.71), and this is the result I get:  
  
```  
For example:  
   German grüßen correctly converted to GRÜßEN, instead of incorrect GRüßEN  
     where ß is replaced with SS  
```  
  
...and now I'm even more confused. What is the actual expected behavior for this case?

---

## Comment 1

> Username: artyom-beilis  
> Created at: 2020-01-13 06:45:30 UTC  
> Url: https://github.com/boostorg/locale/issues/51#issuecomment-573529034  

Make sure boost.locale is compiled with ICU. conversion of ß to SS supported only in ICU backend

---

## Comment 2

> Username: olivier-grech  
> Created at: 2020-01-13 08:55:31 UTC  
> Url: https://github.com/boostorg/locale/issues/51#issuecomment-573563631  

Independently of the result I get by compiling on my machine, could you clarify the discrepancy between the documentation and the example in the code?

---

## Comment 3

> Username: Flamefire  
> Created at: 2023-03-05 16:35:02 UTC  
> Url: https://github.com/boostorg/locale/issues/51#issuecomment-1455140227  

> Independently of the result I get by compiling on my machine, could you clarify the discrepancy between the documentation and the example in the code?  
  
I see the source of the confusion. The part "where ß is replaced with SS" refers to the correct, i.e. expected behavior.  
  
Fixing the example to read:  
>   German grüßen would be incorrectly converted to GRÜßEN, while Boost.Locale converts it to GRÜSSEN  
>     where ß is replaced with SS.  
  
I hope that's easier to understand.

---

## Comment 4

> Username: olivier-grech  
> Created at: 2023-03-21 10:18:40 UTC  
> Url: https://github.com/boostorg/locale/issues/51#issuecomment-1477582315  

This is much more clear, thanks.
