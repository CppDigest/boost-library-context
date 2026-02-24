# #68 - optional<const string &>::value_or returns reference to temp [Open]

> Username: GranBurguesa  
> Created at: 2019-01-22 17:11:40 UTC  
> Updated at: 2020-01-20 14:28:46 UTC  
> Url: https://github.com/boostorg/optional/issues/68  

I'm using `boost::optional<const string &>` and found some pitfalls when using `value_or(char *)`.  
  
Basically since `value_or` takes the "or" party as a template type the conversion from `char *` to `string` happens inside the function call and then a reference to that temporary is returned, leading to UB and in my case a crash.  
  
Furthermore, even if the optional is populated and the alternative isn't use, it seems that some sort of copy of the value is still required because of the use of the `?:` and then a similar crash occurs.  
  
I think a reasonable workaround would be to have the `value_or` for references take the alternative value (`r`) only as `const T &` instead of as a type param. This would require the implicit conversion to happen at the call site and the function would not create a temporary.  
  
This doesn't avoid all possible dangling references but it improves things.  
  
Here is a short reproduction. I'm using boost 1.69, gcc 8.2:  
```c++  
#include <iostream>  
#include <cstdlib>  
#include <boost/optional.hpp>  
  
int main()  
{  
    const std::string input = "a good string";  
    boost::optional<const std::string &> opt = input;  
  
    std::string output = opt.value_or("alternative");  // segfault here  
  
    std::cout << output << std::endl;  
}  
```  
  
Note I do get a warning about returning a reference to a temporary. But usually we disable warnings within boost because there's so much noise.

---

## Comment 1

> Username: akrzemi1  
> Created at: 2019-02-25 07:08:20 UTC  
> Url: https://github.com/boostorg/optional/issues/68#issuecomment-466895722  

Thanks for the report. My apologies for keeping it waiting for a month. Yes, Taking only `const T&` as argument seems to be a good idea. I could also declare the `T&&` overload as deleted, although I am not immediately sure if this does not break reasonable usages.  
The fix will still take a couple of days, as I am traveling right now.

---

## Comment 2

> Username: anshu-khare-design  
> Created at: 2020-01-19 13:11:41 UTC  
> Updated at: 2020-01-19 13:17:04 UTC  
> Url: https://github.com/boostorg/optional/issues/68#issuecomment-576003200  

As far as I can understand, in the second line of code,  
boost::optional<const std::string &> opt = input;  
  
You are storing std::string object into boost::optional<string&> object which is causing the problem.  
You should store std::string object into boost::optional<string> object and you will get the expected output.  
   
    #include <iostream>  
  
    #include <cstdlib>  
    #include <boost/optional.hpp>  
  
    int main()  
    {  
    
    const std::string input = "a good string";  
  
    boost::optional <const std::string> opt = input;            //make changes here       
  
    std::string output = opt.value_or("alternative");    
  
    std::cout << output << std::endl;  
    }

---

## Comment 3

> Username: GranBurguesa  
> Created at: 2020-01-20 14:28:34 UTC  
> Updated at: 2020-01-20 14:28:46 UTC  
> Url: https://github.com/boostorg/optional/issues/68#issuecomment-576298774  

> You are storing std::string object into boost::optional<string&> object which is causing the problem.  
> You should store std::string object into boost::optional object and you will get the expected output  
  
Sure, that would prevent UB here but at the cost of an extra string copy, which I was trying to avoid. If `boost::optional` is going to support references then we might as well make them easy to use correctly.
