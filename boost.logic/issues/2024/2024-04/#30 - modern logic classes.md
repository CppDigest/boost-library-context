# #30 - modern logic classes [Open]

> Username: gpeterhoff  
> Created at: 2024-04-25 17:42:51 UTC  
> Updated at: 2024-05-15 19:55:53 UTC  
> Url: https://github.com/boostorg/logic/issues/30  

Hello,  
I have implemented (single header library) the logic classes bool4, bool3 and bool2:  
- bool4  
https://de.wikipedia.org/wiki/Belnaps_vierwertige_Logik  
https://en.wikipedia.org/wiki/Four-valued_logic  
- bool3 (similar to tribool)  
https://de.wikipedia.org/wiki/Dreiwertige_Logik  
https://en.wikipedia.org/wiki/Three-valued_logic  
- bool2 (similar to bool)  
  
These provide additional support:  
- weak negation https://de.wikipedia.org/wiki/Dreiwertige_Logik#Starke_und_schwache_Negation  
implemented as operator~   
- charconv  
boost::charconv::from_chars/to_chars  
if available (C++17) std::from_chars/to_chars  
- type traits  
boost::is_logic  
- std::numeric_limits  
  
Differences to bool/tribool  
- operator>>(stream) always returns false in the event of an error  
  
Implementation  
- If standard functions/classes are available these are used, otherwise (fallback) boost.  
- I used my small (single header) library streamutil. This simplifies a few things, but is not absolutely necessary.  
- For tribool I added the functions/type traits/limits/operators.  
  
Unfortunately I can't provide bool4/3/2 in the namespace boost::logic because it conflicts with indeterminate.  
  
todo  
- Remove tribool, then the classes can be implemented in boost::logic. For compatibility reasons, "using tribool = bool3;" can be provided (temporarily).  
- For bool4/bool3 there are more logical operators 1.). Perhaps it would be possible to provide these as well. However, I am not a mathematician and cannot judge whether this would make sense, as the functions/operators would have to be provided uniformly for bool4/3/2.  
  
In principle, you should take another look at the code/tables to detect errors :-)  
  
many thanks  
Gero  
  
Code  
[logic2.hpp.txt](https://github.com/boostorg/logic/files/15113419/logic2.hpp.txt)  
[streamutil.hpp.txt](https://github.com/boostorg/logic/files/15113421/streamutil.hpp.txt)  
[is_integer.hpp.txt](https://github.com/boostorg/logic/files/15113547/is_integer.hpp.txt)  
  
1.)  
- bool4  
https://de.wikipedia.org/wiki/Belnaps_vierwertige_Logik#Wahrheitstafeln  
- bool3  
https://de.wikipedia.org/wiki/Dreiwertige_Logik#%C5%813_und_K3  
https://en.wikipedia.org/wiki/Three-valued_logic#Logics

---

## Comment 1

> Username: gpeterhoff  
> Created at: 2024-04-29 19:47:42 UTC  
> Updated at: 2024-05-04 07:02:02 UTC  
> Url: https://github.com/boostorg/logic/issues/30#issuecomment-2083534880  

Hello,  
here is a new version.  
  
Compatibility with tribool  
* **Removed**. All classes/constants/etc are defined in boost::logic. So this **only** works if tribool is **not** included (because of indeterminate).  
  
better  
* Support for charconv is no longer mandatory.  
* Values for logic_style  
  
new  
* boost::charconv::from_chars(boost::core::string_view)  
* std::to_(w)string  
* concept std::logic  
* A very simple std::formatter for std::format.  
  
[logic.hpp.txt](https://github.com/boostorg/logic/files/15208282/logic.hpp.txt)  
  
thx  
Gero

---

## Comment 2

> Username: gpeterhoff  
> Created at: 2024-05-15 19:55:52 UTC  
> Url: https://github.com/boostorg/logic/issues/30#issuecomment-2113350435  

Hello Andrzej,  
  
>> You could also ask why there is e.g. boost::math::quaternion/octonion. I don't know of a specific purpose for this either, but it makes sense to have them generally available.  
  
> Interesting. It looks like we have two quaternion implementations in Boost. The other being in Boost.QVM. It is nicely documented there, along with its motivation (for representing rotations in 3D space. The following thread demonstrates that people want it:  
https://groups.google.com/g/boost-developers-archive/c/gl4GxscZ1OU  
  
I think it's a design flaw to have 2 different implementations of quaternion. Just like boost::string_view and boost:::core::string_view. octonionen does not seem to be affected.  
This should be consolidated. But that's not the point here.  
  
> So it looks like you are saying that you wanted to provide a better tribool and bool2 and bool4 are just a byproduct of this endeavour. Is that right?  
  
There are conversion constructors with which you can control the exact behavior. In this sense, bool2 is actually a helper class.  
  
> For bool3 I can at least imagine a use case (true false and "I don't know"), but the docs do not give us any example that would show how the third state would be called.  
> For `bool4` I do not even imagine how fthe four states would be called. The literature that you provide (the part that is not in german) offers at least two different models for four-state bool logic. What nming did you choose? Can the documentation show it?  
  
> The above description says that for a three-value logic there are three kinds of negation: the standard one, the weak, and the strong. Give the rationale (in the documentation) why you provide only two.  
  
> You use the two operators for the two negations. But your choice of symbols is absolutely arbitrary. It would be less error-prone if the other negations were named functions.  
  
> Also, the documentation should say why you decided to put all the negation operations in the same type rather than offering three different types with different negation semantics. It is not clear to me if any use case requires all of these negation types together.  
  
These are also my questions. In principle, there are 3 different negations (standard, weak, strong), but only 2 meaningful C++ operators (! ~) with which this could be mapped. How should this be handled ?  
bool4 contains even more operations, but these would have to be implemented as a function.  
As I said, I'm not a mathematician. With bool4 I followed Belnap.  
I would like to discuss whether all this is correct.  
  
The german wikipedia entries are more detailed, which is why I have included them.  
  
regards  
Gero
