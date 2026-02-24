# #45 - Extra array level when serializing member value [Closed]

> Username: m8mble  
> Created at: 2020-02-23 21:10:41 UTC  
> Updated at: 2020-02-27 18:42:50 UTC  
> Closed at: 2020-02-27 18:42:50 UTC  
> Url: https://github.com/boostorg/json/issues/45  

I'm observing weird output when testing with the following condensed minimal program:  
  
```  
#define BOOST_JSON_HEADER_ONLY 1  
#include <iostream>  
#include <boost/json.hpp>  
  
class Wrapper  
{  
public:  
   Wrapper(boost::json::value v) : wrapped{v} {}  
  
   boost::json::value wrapped;  
};  
  
int main() {  
   const auto input = boost::json::string_view{"[]"};  
   const auto value = boost::json::parse(input);  
   const auto wrapper = Wrapper{value};  
   const auto output = boost::json::to_string(wrapper.wrapped);  
   std::cout << input << " -> " << boost::json::to_string(wrapper.wrapped) << std::endl;  
}  
```  
  
I'd expect output `[] -> []` but I'm seeing `[] -> [[]]`. If I skip using the `Wrapper`, I get the desired output. Am I violating some requirement of the `value` type?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-02-24 00:36:45 UTC  
> Url: https://github.com/boostorg/json/issues/45#issuecomment-590136333  

Now that's interesting! I expect `wrapped(v)` to invoke the copy constructor, not to nest the thing in an array. I will investigate, thanks.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-02-24 01:53:11 UTC  
> Url: https://github.com/boostorg/json/issues/45#issuecomment-590144522  

Hmm... I added a test for this case, and it seems to pass. Can you take a look please?  
https://github.com/vinniefalco/json/pull/46/commits/f39934684a52196296b1429359342659a54eccbd#diff-208eebe138334cc681ac37d611982389R446

---

## Comment 3

> Username: m8mble  
> Created at: 2020-02-24 17:08:09 UTC  
> Url: https://github.com/boostorg/json/issues/45#issuecomment-590441028  

Thanks for looking into it. Your test indeed passes.  
  
Now it gets weird: I was using `g++ (Arch Linux 9.2.1+20200130-2) 9.2.1 20200130` to submit this issue.  And still I'm observing the reported behavior consistently. With `clang 9.0.1` though, my original bug vanishes. So this issue appears to be compiler dependent. Surprising! More surprisingly, your `testIssue45` succeed with both compilers.   
  
Next step: I narrowed down the differences between your version and mine. The crucial difference is the initialization of the `jv` member. I'm using curly braces `jv{jv_}`, you used round braced `jv(jv_)`. With curly braces, your `testIssue45` performs as reported by me (with `g++` only).  
  
Obviously, my version could use the `initializer_list` constructor. I'm assuming this it was causes the extra array level. In the end, final question: Which compiler is correct?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-02-24 18:32:19 UTC  
> Url: https://github.com/boostorg/json/issues/45#issuecomment-590481203  

C++ is a bit unfriendly to classes with constructors that take `initializer_list`. And the main types in this library (`array`, `object`, and `value`) can all be constructed from initializer lists. The semantics of the library are consistent across all the containers: construction from an initializer list will produce either an array or an object with the contents of the list. In your case it is producing an array (because your initializer-list does not match the pattern of key/value pairs).  
  
I don't think the problem is with the compiler, I think the problem is your code. Specifically, you should use function call syntax not brace initialization to construct the objects, otherwise the meaning is ambiguous. You can thank the mess of C++ initializers for this.

---

## Comment 5

> Username: pdimov  
> Created at: 2020-02-24 18:40:55 UTC  
> Url: https://github.com/boostorg/json/issues/45#issuecomment-590485289  

Container-like types, when initialized with braces, generally construct a container with the given elements. You should use parentheses to copy-construct.  
  
Given `jv{ x }`, there's no good way to disambiguate between array-of-one-x and copy-of-x; the array interpretation is usually chosen for consistency with `jv{ x1, x2 }`, so that pack expansions such as `jv{ x... }` don't change meaning depending on whether the pack happens to have size 1.

---

## Comment 6

> Username: m8mble  
> Created at: 2020-02-24 18:42:05 UTC  
> Url: https://github.com/boostorg/json/issues/45#issuecomment-590485829  

Agreed, I shouldn't be using the brace initialization syntax. And yes, the API is consistent as is. I see that. Sorry for alarming you.  
  
But shouldn't the / all compilers agree on a result? At least the mess of C++ initializers is the same for all of them...

---

## Comment 7

> Username: vinniefalco  
> Created at: 2020-02-24 18:43:01 UTC  
> Updated at: 2020-02-24 18:43:09 UTC  
> Url: https://github.com/boostorg/json/issues/45#issuecomment-590486274  

> But shouldn't the / all compilers agree on a result?  
  
I don't really know. Perhaps not, but so what?

---

## Comment 8

> Username: m8mble  
> Created at: 2020-02-24 18:46:26 UTC  
> Updated at: 2020-02-24 18:46:36 UTC  
> Url: https://github.com/boostorg/json/issues/45#issuecomment-590487793  

> I don't really know. Perhaps not, but so what?  
  
If `jv{jv_}` is supposed to be a nested array via the initializer_list constructor, that should apply to modern `clang` as well. I get it, its not your fault. At least you know now...

---

## Comment 9

> Username: pdimov  
> Created at: 2020-02-24 18:52:19 UTC  
> Url: https://github.com/boostorg/json/issues/45#issuecomment-590490292  

It's possible that something in the initialization rules got changed in the standard, and the latest Clang has implemented the change.

---

## Comment 10

> Username: m8mble  
> Created at: 2020-02-24 18:58:09 UTC  
> Url: https://github.com/boostorg/json/issues/45#issuecomment-590493351  

I was using `--std=c++17` just to rule this out.

---

## Comment 11

> Username: madmongo1  
> Created at: 2020-02-25 10:32:34 UTC  
> Url: https://github.com/boostorg/json/issues/45#issuecomment-590798597  

The initialisation rules are quite clear (and unsafe IMHO).  
The compiler will favour a list initialiser over any other constructor argument, even if it’s a list initialiser of a type that goes through a conversion to satisfy the constructor.  
This bit me on the arse when using nlohmann json.  
List initialisers are evil.  
Curly braces for object construction should never be used unless you specifically want the list initialisation.

---

## Comment 12

> Username: vinniefalco  
> Created at: 2020-02-27 18:42:50 UTC  
> Url: https://github.com/boostorg/json/issues/45#issuecomment-592114429  

I'll go ahead and close this since it isn't a bug - feel free to reopen or create a new issue if you believe it is unresolved, thanks!
