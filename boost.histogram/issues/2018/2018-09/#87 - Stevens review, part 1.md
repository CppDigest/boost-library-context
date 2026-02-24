# #87 - Stevens review, part 1 [Closed]

> Username: HDembinski  
> Created at: 2018-09-18 07:48:09 UTC  
> Updated at: 2018-10-10 14:18:16 UTC  
> Closed at: 2018-10-10 14:18:16 UTC  
> Url: https://github.com/boostorg/histogram/issues/87  

abstract.html:  
  
- "the one- and multi-dimensional case"  
 "cases" should be plural.  
  
- "..submit this project to Boost, that's why..."  
 Comma splice  
  
- Please don't overuse *bold text*  
  
acknowledgments.html  
  
- "converting the documentation, and adding Jamfiles"  
 No comma here.  
  
motivation.html  
  
- "The GNU Scientific Library (GSL) and in the ROOT framework"  
 Remove "in"  
  
- "The C implementations of the GSL are elegant..."  
 Are there multiple implementations?  
  
- "The implementations are not customizable, you have to..."  
 Comma splice  
  
build.html  
  
- Is there a reason that CMakeLists.txt is in  
 build/ instead of test/?  
  
getting_started.html  
  
- "int main(int, char**) {"  
 If you're not using the parameters, `int main()` is a  
 valid signature.  
  
- Since you require C++11, you might as well use <random> instead  
 of Boost.Random.  The Boost implementation of mt19937 has better  
 performance, but that doesn't really matter here.  
  
- "in addition, the factory also accepts iterators over a sequence of  
 axis::any, the polymorphic type that can hold concrete axis types"  
 The relationship between axis::any and axis::any_std  
 (the type actually used) in unclear.  
  
guide.html:  
  
- "A histogram consists a number of..."  
 "consists /of/ a number of..."  
  
- "which provides safe counting, is fast and memory efficient."  
 There are only two elements, so use "and" instead of a comma  
 or say "is fast, and /is/ memory efficient."  
  
- "Axes objects with different label do..." ->  
 s/Axes/Axis/, s/label/labels/  
  
- "...sequence of bin indices for each axes in order."  
 s/axes/axis/  
  
- I wonder whether you could handle the issue that  
 h * 2 != h + h, by using h * weight(2) to make it  
 clear that multiplication changes the weights of  
 the samples instead of changing the number of samples.  
  
- "...remove some axes and look the equivalent lower..."  
 "...look /at/ the equivalent..."  
  
- "so it is not worth keeping that axies around"  
 s/axies/axis/  
  
- "Users can create new axis classes ..., or implemented..."  
 s/implemented/implement/  
  
- "Here is a contrieved example of a..."  
 s/contrieved/contrived/  
  
- "The guarantees ... are only valid, if the default..."  
 No comma.  
  
- "... you need know what you are doing"  
 "you need /to/ know"  
  
benchmarks.html:  
  
- No GSL?  
  
- You say uniform and normal distributions, but there's  
 only a single plot.  Does this mean that you are putting  
 both uniform and normal variates into a single histogram?  
  
rationale.html  
  
- "Axis access"  
 Not the most fortuitous word combination.  
  
- "The buildin storage types..."  
 s/buildin/builtin/  
  
- It seems that using weighted fills breaks the special  
 overflow handling properties of adaptive_storage.  I  
 think that this deserves a distinct note.  
  
- "Why is Boost.Histogram not build on top..."  
 s/build/built/  
  
concepts.html:  
  
- Concept names use CamelCase, by convention.  
  
- It doesn't seem very sane to have the value_type  
 be a reference.  Is there any reason for this  
 other than the fact that you specify the exact  
 signature of axis_type::index?  
  
- I would prefer to set the size of a storage_type  
 in the constructor instead of using reset, if  
 that's possible.  
  
- I don't understand the difference between the two  
 overloads of storage_type::add.  
  
reference.html:  
  
- Unfortunately forward declarations seem to mess  
 up the doxygen/boostbook toolchain.  As a result  
 a number of components are listed in histogram_fwd.hpp  
 I'll look into this.  
  
- I'll deal with the reference content along with the  
 code.  I will note that the reference documentation  
 seems a bit lacking overall as there are many components  
 without any description.  
  
General:  
  
- The passive voice is overused.

---

## Comment 1

> Username: HDembinski  
> Created at: 2018-10-10 14:18:13 UTC  
> Url: https://github.com/boostorg/histogram/issues/87#issuecomment-428590023  

fixed on develop
