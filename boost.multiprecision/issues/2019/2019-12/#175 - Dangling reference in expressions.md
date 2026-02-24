# #175 - Dangling reference in expressions [Closed]

> Username: spoofedex  
> Created at: 2019-12-11 09:42:56 UTC  
> Updated at: 2020-04-06 21:04:20 UTC  
> Closed at: 2020-04-06 21:04:20 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/175  

Hi all,  
  
I've had a problem with code resembling the following crashing when compiling in release mode:  
```  
  namespace mp = boost::multiprecision;  
  const auto value1  = mp::pow(mp::cpp_int(a), b);  
  const auto value2  = mp::pow(mp::cpp_int(c), d);  
  return value1 <= value2;  
```  
  
The cause seems to be that multiprecision's "pow" takes const references to the first parameter, which is passed (as reference) to "detail::expression", which stores it as a expression_storage_base<...>::type, which is a reference to the type in case of a cpp_int.  
  
So "value1" contains a reference to the temporary, which is destroyed immediately. The comparison evaluates the expression, which causes the crash caused by the dangling reference.  
  
This issue can be worked around by manually extending the lifetime of the cpp_int.  
  
My recommendation would be to accept rvalue-references, and moving them to the storage. Otherwise, I'd say that an "= delete" of an overload that takes an rvalue-reference and documentation of the behaviour (if it does not yet exist; I could not find it) would be appropriate.  
  
What are your thoughts? Thanks in advance!

---

## Comment 1

> Username: pabristow  
> Created at: 2019-12-13 15:15:40 UTC  
> Updated at: 2019-12-13 15:22:14 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/175#issuecomment-565478045  

As this item  
[https://stackoverflow.com/questions/40952097/strange-boost-cpp-int-behaviour-when-using-auto-type-deduction](url)  says, quoting the Boost.Multiprecision manual:  
  
[https://www.boost.org/doc/libs/1_72_0/libs/multiprecision/doc/html/boost_multiprecision/intro.html](url)  
  
even specifically mentions the pitfall of using auto to store an expression template:  
  
"Caution  
  
In C++11 you should never store an expression template using:      
             auto my_expression         = a + b -  c;   
 unless you're absolutely sure that the lifetimes of a,  b and c  will outlive that of my_expression.         
  
In fact, it is particularly easy to create dangling references by mixing  expression templates with the auto keyword, for example:  auto val  = cpp_dec_float_50("23.1") * 100;    
In this situation, the integer literal is stored directly in the expression  template - so its use is OK here - but the cpp_dec_float_50   temporary is stored by reference and then destructed when the statement completes,   leaving a dangling reference.   
  
 If in doubt, do not ever mix expression templates  with the auto keyword."  
  
Patient: Doctor, Doctor, I get garbage when I use auto and expression templates. together.  
Doctor: Don't do that!

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-12-13 17:31:59 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/175#issuecomment-565532634  

>Patient: Doctor, Doctor, I get garbage when I use auto and expression templates. together.  
Doctor: Don't do that!  
  
This is true, however in this particular case, we can probably fix this.  
  
BTW, elsewhere we've been updating our performance results, and expression templates appear to have questionable benefits for the latest crop of compilers.  It still needs more investigation, but you *may* find your code runs faster with expression templates turned off.

---

## Comment 3

> Username: ckormanyos  
> Created at: 2019-12-13 21:51:54 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/175#issuecomment-565621758  

> It still needs more investigation, but you may find your code runs faster with expression templates turned off.  
  
Nod. My personal experience has shown very little or almost no significant *speed* differences resulting from ET either on/off.
