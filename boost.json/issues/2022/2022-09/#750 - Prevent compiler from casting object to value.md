# #750 - Prevent compiler from casting object to value [Closed]

> Username: matty0ung  
> Created at: 2022-09-03 07:10:02 UTC  
> Updated at: 2023-07-17 15:57:35 UTC  
> Closed at: 2023-07-17 15:57:35 UTC  
> Url: https://github.com/boostorg/json/issues/750  

Not strictly a bug, but would it be feasible in Boost.JSON to explicitly disallow automatic casting from boost::json::object to boost::json::value?  At the moment, even though boost::json::object is not AFAICT a subclass of boost::json::value, the compiler happily allows it (eg you can pass a reference to a boost::json::object as a parameter to a function that takes a reference to a boost::json::value) and the code compiles without warning but you get garbage results at runtime.  
  
I know I can avoid the problems by checking that I don't ever pass a boost::json::object where a boost::json::value is required, but it would be great to make things more idiot proof for library users such as me.  
  
(I'm using Boost version 1.79 on Ubuntu Linux with GCC 11.2 compiler BTW.)

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-09-03 07:16:34 UTC  
> Url: https://github.com/boostorg/json/issues/750#issuecomment-1236065360  

Can you provide an example of code that leads to garbage at runtime? The conversion from object to value should work properly.

---

## Comment 2

> Username: matty0ung  
> Created at: 2022-09-04 17:36:40 UTC  
> Url: https://github.com/boostorg/json/issues/750#issuecomment-1236385448  

Hi,  
I've tried to extract the relevant bits from my application in the code below.  
```  
class MyClass {  
public:  
  
   MyClass(boost::json::value const & recordData) : recordData{recordData} {return; }  
...  
   void load() {  
      std::cout << "Loading record containing" << this->recordData.as_object().size() << "elements";  
...  
   }  
...  
protected:  
   boost::json::value const & recordData;  
  
};  
  
  
// Calling code  
boost::json::value & inputDocument;  
...  
boost::json::object & documentRoot = inputDocument.as_object();  
boost::json::value & rootRecordData = documentRoot["beerjson"];  
boost::json::object & rootRecordDataObject = rootRecordData.as_object();  
  
// This logs correctly - eg "Root record contains 2 elements"  
std::cout << "Root record contains" << rootRecordDataObject.size() << "elements";  
  
MyClass myClassWorking{rootRecordData};  
myClassWorking.load(); // Logs correct info - eg "Loading record containing 2 elements"  
  
MyClass myClassBroken{rootRecordDataObject};  
myClassBroken.load(); // Logs garbage info - eg "Loading record containing 1732749930 elements"  
```  
I'm entirely prepared to believe I'm doing something idiotic, but I can't immediately see what it is!

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-04 17:56:52 UTC  
> Updated at: 2022-09-04 17:57:25 UTC  
> Url: https://github.com/boostorg/json/issues/750#issuecomment-1236388061  

When you acquire a reference to an element, the reference can become invalidated if you modify the container it came from. Are you changing a `json::value` in `load`?

---

## Comment 4

> Username: matty0ung  
> Created at: 2022-09-04 18:18:43 UTC  
> Updated at: 2022-09-04 18:18:54 UTC  
> Url: https://github.com/boostorg/json/issues/750#issuecomment-1236390969  

As far as I can tell I am not changing anything.  It is certainly not my intent, as I am only trying to read the file, not modify it (hence why recordData is a const reference).  
  
I changed the other variables to const (which means using `*documentRoot.if_contains("beerjson")` instead of `documentRoot["beerjson"]` etc) but I got the same results.  
  
Is there any increased logging or diagnostics I can turn on to flag when a container is getting modified?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-09-04 18:22:42 UTC  
> Updated at: 2022-09-04 18:25:52 UTC  
> Url: https://github.com/boostorg/json/issues/750#issuecomment-1236391520  

Well where is `inputDocument` coming from? If someone modifies that container, it doesn't matter that you hold a const reference to it, things will become invalidated.  
  
If you could reproduce this as a self-contained example on compiler-explorer it would be much easier to diagnose:.  
  
You can include boost headers if you press the Libraries button and add Boost, e.g.: https://godbolt.org/z/66G56Y49o

---

## Comment 6

> Username: matty0ung  
> Created at: 2022-09-04 19:07:57 UTC  
> Url: https://github.com/boostorg/json/issues/750#issuecomment-1236398053  

I hear what you're saying.  I would love to give a self-contained example, but I'm using the Qt framework so it's non-trivial to change everything to use only libraries available on godbolt.org.  
  
That said, I think I can convince myself that `inputDocument` is not invalidated by adding a log line at the end:  
```  
class MyClass {  
public:  
  
   MyClass(boost::json::value const & recordData) : recordData{recordData} {return; }  
...  
   void load() {  
      std::cout << "Loading record containing" << this->recordData.as_object().size() << "elements";  
...  
   }  
...  
protected:  
   boost::json::value const & recordData;  
  
};  
  
  
// Calling code  
boost::json::value & inputDocument;  
...  
boost::json::object & documentRoot = inputDocument.as_object();  
boost::json::value & rootRecordData = documentRoot["beerjson"];  
boost::json::object & rootRecordDataObject = rootRecordData.as_object();  
  
// This logs correctly - eg "Root record contains 2 elements"  
std::cout << "Root record contains" << rootRecordDataObject.size() << "elements";  
  
MyClass myClassWorking{rootRecordData};  
myClassWorking.load(); // Logs correct info - eg "Loading record containing 2 elements"  
  
MyClass myClassBroken{rootRecordDataObject};  
myClassBroken.load(); // Logs garbage info - eg "Loading record containing 1732749930 elements"  
  
// This logs correctly - eg "At the end, root record contains 2 elements"  
std::cout << "At the end, root record contains" << rootRecordDataObject.size() << "elements";  
  
```  
  
I'll keep plugging away at my code, making sure I avoid the casts, and see if I hit any other errors.  That might throw light on whether I'm doing something wrong with object lifetimes etc.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-09-04 19:20:41 UTC  
> Url: https://github.com/boostorg/json/issues/750#issuecomment-1236399835  

OH... I see the problem. If `MyClass::MyClass( value const &)` is constructed from `object`, then the class will be retaining a reference to a temporary. To prevent this from happening, change your parameter types to pointers. This will give you a compiler error if you attempt to pass a temporary to the class:  
```  
MyClass::MyClass( json::value const* jv )  
  : recordData( *jv )  
  {  
  }  
```  
  
I had to do the same thing in the serializer, for the same reason:  
https://github.com/boostorg/json/blob/2cbc263ced1e2e8151fd8096e1f4e8b87eaeaa28/include/boost/json/serializer.hpp#L165

---

## Comment 8

> Username: matty0ung  
> Created at: 2022-09-04 19:55:15 UTC  
> Url: https://github.com/boostorg/json/issues/750#issuecomment-1236404898  

Ah, OK, thanks, I think I see.  We are implicitly constructing a temporary json::value from the json::object and then that json::value goes out of scope, the reference to it is invalid?

---

## Comment 9

> Username: grisumbras  
> Created at: 2023-07-17 15:57:35 UTC  
> Url: https://github.com/boostorg/json/issues/750#issuecomment-1638425926  

Since the problem has been resolved, I am closing the issue.
