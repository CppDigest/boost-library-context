# #1211 - Review 2: generic Programming docs [Closed]

> Username: jzmaddock  
> Created at: 2025-11-05 19:15:54 UTC  
> Updated at: 2026-01-29 15:22:52 UTC  
> Closed at: 2025-11-06 15:53:50 UTC  
> Url: https://github.com/boostorg/decimal/issues/1211  

>Same comment about the `float_equal` function not being the right algorithm.  
Please consider an example that does not need such a controversial function.  
  
As before, rename at the very least.  
  
>Please add text before the code that explain what this example aims to  
demonstrate.  I assume that it is the use of `using` plus ADL pattern/idiom.  
  
Yup, I think this needs to explicitly say that std lib functions are in namespace boost decimal, and therefore to write generic code that works with builtin types as well as UDT's we need to make all std lib calls unqualified, and make sure that they can either be found via ADL (for UDT's) or are brought in via a using declaration.  The idiom is not terribly well known and there are lots of traps for the unwary when using it, so some careful hand holding is required.  
  
How about something like:  
  
When writing generic code that will work equally well for both builtin types and user defined types we must remember that:  
  
* The builtin types such as `float`, `double` etc have all their standard library functions declared in `namesapace std`, but  
* User defined types are not allowed to use `namespace std` and therefore define their standard library functions in their own namespace, in this case `namespace boost::decimal`.  
  
Therefore when making a call to a standard library function, we must:  
  
* Make sure that the call is unqualified so that the user defined type finds the correct overload via ADL, and  
* Make sure there is a using declaration in function scope, which makes the `namespace std` version of the function available for builtin types, and  
* Remember not to be tempted to take a short cut by adding a `using namespace std;` to the function scope, as this can cause a whole heap of inscrutable problems, and  
* Remember that if we forget to add the `using std::whatever;` declaration, the code may well compile without warnings or errors, but will silently call `::whatever` rather than `::std::whatever` for example, which will behave incorrectly for type `long double`.  
  
This may well be overkill, but we might as well hit them over the head with the pitfalls I guess ;)
