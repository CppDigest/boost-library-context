# #161 Add BOOST_CXX14_CONSTEXPR to CV assign() [Merged]

> Username: rogerorr  
> Created at: 2020-07-08 13:34:43 UTC  
> Updated at: 2020-07-08 15:42:06 UTC  
> Merged at: 2020-07-08 15:41:25 UTC  
> Closed at: 2020-07-08 15:41:25 UTC  
> Url: https://github.com/boostorg/date_time/pull/161  

Without this change the BOOST_CXX14_CONSTEXPR on the constructors has no  
effect, as they invoke assign()

---

## Comment 1

> Username: JeffGarland  
> Created_at: 2020-07-08 15:01:56 UTC  
> Url: https://github.com/boostorg/date_time/pull/161#issuecomment-655574798  

Hi Roger -- I did try this out and I was unable to get this to resolve at compile time with gcc at least.  I believe that is a result of the exception paths that are used by the higher order objects for handling out of range values.  I think to make this really work there needs to be if constexpr logic to avoid exceptions in the compile time case -- which is a bit more involved.  So I can merge the change, but I don't think it's having the impact you're looking for.

---

## Comment 2

> Username: rogerorr  
> Created_at: 2020-07-08 15:14:23 UTC  
> Url: https://github.com/boostorg/date_time/pull/161#issuecomment-655582360  

That's odd. The execption paths shouldn't matter when being evaluated in a constexpr context (as long as they're not taken of course.)  
  
My trivial test case:  
```  
#include <boost/date_time.hpp>  
  
static constexpr boost::posix_time::ptime UNIX_EPOCH(boost::gregorian::date(1970, 1, 1));  
```  
  
Using ```g++ -std=c++17 trivial_test.cpp```  
  
Without this change:  
```  
constrained_value.hpp:48:13: error: call to non-'constexpr' function 'void boost::CV::constrained_value<value_policies>::assign(boost::CV::constrained_value<value_policies>::value_type) [with value_policies = boost::CV::simple_exception_policy<short unsigned int, 1400, 9999, boost::gregorian::bad_year>; boost::CV::constrained_value<value_policies>::value_type = short unsigned int]'  
   48 |       assign(value);  
      |       ~~~~~~^~~~~~~  
```  
  
With this change it compiles successfully.  
  
(I'm using gcc trunk from 20200601)

---

## Comment 3

> Username: mclow  
> Created_at: 2020-07-08 15:17:39 UTC  
> Url: https://github.com/boostorg/date_time/pull/161#issuecomment-655584367  

What Roger said (about constexpr and exceptions).   
I wrote about this on SO: https://stackoverflow.com/questions/33716507/how-can-this-code-be-constexpr-stdchrono

---

## Comment 4

> Username: JeffGarland  
> Created_at: 2020-07-08 15:39:03 UTC  
> Url: https://github.com/boostorg/date_time/pull/161#issuecomment-655597055  

Ok interesting - today I learned again :)    
  
Looking back on my changes I was testing a bit more directly.   
  
```  
  //test_constrained_value.cpp  
  
 class day_value_policies  
 {  
  public:  
    typedef unsigned int value_type;  
    static unsigned int min BOOST_PREVENT_MACRO_SUBSTITUTION () { return 0; };  
    static unsigned int max BOOST_PREVENT_MACRO_SUBSTITUTION () { return 31;};  
    static void on_error(unsigned int&, unsigned int, boost::CV::violation_enum)  
    {  
      throw bad_day();  
   }  
  };  
...  
  //check constexpr case  
  constexpr constrained_value<day_value_policies> cv3(1);  
  static_assert(cv3 == 1, "constexpr construction/conversion");  
  check("constexpr constrained value construct and equal", true);  
```  
  
And now that I'm looking again I see the compiler is telling me exactly the problem here: min and max, which are executed need to be constexpr.  So looking at greg_month and how this is used upstream it indeed looks like it will indeed work.

---

## Comment 5

> Username: rogerorr  
> Created_at: 2020-07-08 15:41:22 UTC  
> Url: https://github.com/boostorg/date_time/pull/161#issuecomment-655598320  

Good -- that all makes sense. And you've already learned something today so you're ahead :)

---

## Comment 6

> Username: JeffGarland  
> Created_at: 2020-07-08 15:42:06 UTC  
> Url: https://github.com/boostorg/date_time/pull/161#issuecomment-655598749  

lol - c++ is a life long learning project :)

---
