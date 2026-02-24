# #103 - boost::python::objects::add_cast can make incorrect connections [Open]

> Username: robpieke  
> Created at: 2017-02-02 23:14:31 UTC  
> Updated at: 2018-10-10 09:14:27 UTC  
> Url: https://github.com/boostorg/python/issues/103  

(this was previously logged on trac: https://svn.boost.org/trac/boost/ticket/12807 before I noticed boost::python issues are meant to be reported on github)  
  
We seem to be fairly unique in using add_cast (at least I couldn't find many other reports on Google), so maybe we're not playing by the rules. However.....  
  
We've observed that when adding lots of casting relationships via add_cast, sometimes the relationships aren't setup properly.  
  
I've narrowed this down to demand_types() in inheritance.cpp  
  
It appropriately reserves space to avoid overall reallocation for the type_index vector, however as best I can tell, demand_type() will still shift elements in the vector when it calls insert() with an iterator in the middle of the vector. As a result, we're observing:  
  
- demand_types() calls demand_type() to set 'first'  
- demand_types() calls demand_type() to set 'second'  
  - this call to demand_type() calls insert() which shifts the element that 'first' was previously pointing at  
  
As a result, 'first' ends up pointing at the wrong element, and the wrong pair of vertices are connected in the adjacency_list, effectively setting up a bogus casting relationship.  
  
The eventual impact is that users' python scripts start failing with error messages about their arguments not matching the C++ signature.  
  
Maybe we're breaking a contract by calling add_cast without somehow registering both end-points into the adjacency_list and, if so, I'd love to get some best-practice guidance.  
  
If not, and this is a legitimate issue, here's the patch we're putting in place for the time being:  
  
```  
    type_index_t::iterator first = demand_type(t1);  
    type_index_t::iterator second = demand_type(t2);  
+   first = demand_type(t1);  
```  
  
The first two (unchanged) calls still ensure both types have associated vertices, and the last (new) call compensates for the possibility that the second call invalidated the result from the first call.  
  
I hope this report is useful. It's my first, so please let me know if I can provide any more info! I'll try to upload a repro case later if I can extract it from our production codebase.

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-02-02 23:18:51 UTC  
> Url: https://github.com/boostorg/python/issues/103#issuecomment-277116041  

Hi Rob,  
thanks for the report. It would help me a lot if you could attach a minimal test case demonstrating the issue you are observing.  
Thanks,

---

## Comment 2

> Username: robpieke  
> Created at: 2017-02-02 23:29:26 UTC  
> Url: https://github.com/boostorg/python/issues/103#issuecomment-277118258  

I'll see what I can do! It's a pretty big piece of our existing production codebase that I wasn't involved in writing, so the surgery might take a bit of time. Or maybe it'll be easy ... we'll see :)    
Honestly, boost::python is brand new to me, so I can't write something from scratch unfortunately ... I need to use our existing code as a template.

---

## Comment 3

> Username: robpieke  
> Created at: 2017-02-03 00:51:17 UTC  
> Updated at: 2017-02-03 08:10:47 UTC  
> Url: https://github.com/boostorg/python/issues/103#issuecomment-277133264  

Okay, turned out to be easier than I thought to reproduce. Not sure if this well reflects our production code or not, but it definitely demonstrates the issue:  
  
bug.cpp  
```  
#include <boost/python.hpp>  
  
struct A{}; struct AA{};  
struct B{}; struct BB{};  
struct C{}; struct CC{};  
struct D{}; struct DD{};  
struct E{}; struct EE{};  
  
void* cast(void *src) { return src; }  
  
void doNothingA(A*) {}  
void doNothingB(B*) {}  
void doNothingC(C*) {}  
void doNothingD(D*) {}  
void doNothingE(E*) {}  
  
BOOST_PYTHON_MODULE(bug)  
{  
	boost::python::class_<AA>("AA");  
	boost::python::class_<BB>("BB");  
	boost::python::class_<CC>("CC");  
	boost::python::class_<DD>("DD");  
	boost::python::class_<EE>("EE");  
  
	boost::python::def("doNothingA", doNothingA);  
	boost::python::def("doNothingB", doNothingB);  
	boost::python::def("doNothingC", doNothingC);  
	boost::python::def("doNothingD", doNothingD);  
	boost::python::def("doNothingE", doNothingE);  
  
	boost::python::objects::add_cast(typeid(AA), typeid(A), cast, false);  
	boost::python::objects::add_cast(typeid(BB), typeid(B), cast, false);  
	boost::python::objects::add_cast(typeid(CC), typeid(C), cast, false);  
	boost::python::objects::add_cast(typeid(DD), typeid(D), cast, false);  
	boost::python::objects::add_cast(typeid(EE), typeid(E), cast, false);  
  
	// ENABLING THIS BLOCK IS *ROUGHLY* THE SAME AS THE PATCH MENTIONED EARLIER  
	// (but can be done here at the client level, not requiring a change to boost)  
	// (the patch mentioned earlier ensures a "good graph",  
	//  the duplicated code below just ensures we have all the connections we want,  
	//  but the graph will still have the bad connections as well)  
	#if 0  
	boost::python::objects::add_cast(typeid(AA), typeid(A), cast, false);  
	boost::python::objects::add_cast(typeid(BB), typeid(B), cast, false);  
	boost::python::objects::add_cast(typeid(CC), typeid(C), cast, false);  
	boost::python::objects::add_cast(typeid(DD), typeid(D), cast, false);  
	boost::python::objects::add_cast(typeid(EE), typeid(E), cast, false);  
	#endif  
}  
```  
  
test.py  
```  
import bug  
bug.doNothingA(bug.AA())  
bug.doNothingB(bug.BB())  
bug.doNothingC(bug.CC())  
bug.doNothingD(bug.DD())  
bug.doNothingE(bug.EE())  
```  
  
As-is, I see:   
```  
Traceback (most recent call last):  
  File "test.py", line 3, in <module>  
    bug.doNothingB(bug.BB())  
Boost.Python.ArgumentError: Python argument types in  
    bug.doNothingB(BB)  
did not match C++ signature:  
    doNothingB(B*)  
```  
  
Hopefully you'll see the same, but if not with B at least with C or D or E.  
  
If I enable the ifdef'ed code (which is a straight copy-and-paste of the code it follows), then the python script runs just fine.

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2017-02-03 15:06:34 UTC  
> Url: https://github.com/boostorg/python/issues/103#issuecomment-277270242  

That is...curious indeed. I can reproduce your observation, but will have to look into it to understand what is going on.  
Can you explain what you are trying to achieve there ? How are 'A' and 'AA' really related ?

---

## Comment 5

> Username: robpieke  
> Created at: 2017-02-03 18:00:36 UTC  
> Updated at: 2017-02-03 18:01:31 UTC  
> Url: https://github.com/boostorg/python/issues/103#issuecomment-277317301  

Hopefully my initial report will quickly get you well into the debugging. I'm pretty confident with everything I've mentioned ... up to the point where I admit a lack of insight as to how the insertion point for the vector.insert() call in demand_type() is determined.  
  
As for motivation etc, let's imagine that A and AA are different 3D Vector classes (from different third party libraries), which are easy for me to convert between. And let's imagine that both third party libraries offer interesting functions that operate on their specific Vector3. I want to expose *one* Vector3 class, but functionality from both libraries.  
  
Does that make sense?

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2017-02-03 18:08:09 UTC  
> Url: https://github.com/boostorg/python/issues/103#issuecomment-277319146  

Yeah, that makes sense. (I haven't used custom converters before, so this is somewhat new territory to me, but your use-case gives good rationale for it. The test case alone is enough I think to debug this, and anything beyond such as the vector insertion you are referring to would only obstruct that, so I'll focus on the smaller test case.)

---

## Comment 7

> Username: robpieke  
> Created at: 2017-02-03 18:12:34 UTC  
> Url: https://github.com/boostorg/python/issues/103#issuecomment-277320202  

The test case I sent will see you quickly ending up at the vector insertion ... that's what's messing things up :)  
  
Anyway, I'll leave it with you from here, and I look forward to the conclusion! For now we'll proceed with the patch mentioned at the top.  
  
Thanks!

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2017-02-03 19:37:42 UTC  
> Url: https://github.com/boostorg/python/issues/103#issuecomment-277341651  

Quite possible. However, I doubt that the `add_cast` function was ever meant to be used for what you are trying to do. After all, conversion between two custom types isn't really in scope. I suppose the expected logic would involve registering custom converters directly to and from Python.  
So, if you have registered your type 'AA' with a Python wrapper type 'pAA', you'd register custom converters to convert between 'pAA' and 'A', rather than between 'AA' and 'A'.  
That being said, I still want to understand why calling `add_cast` twice for a given (src,dest) pair "resolves" the issue you are observing.

---

## Comment 9

> Username: robpieke  
> Created at: 2017-02-03 21:56:42 UTC  
> Updated at: 2017-02-03 22:00:19 UTC  
> Url: https://github.com/boostorg/python/issues/103#issuecomment-277373198  

Calling it twice resolves the issue because once all the vertices are in the graph (after the first set of calls), insert() never gets called any more, so demand_types() always returns the right values and the correct edges are inserted in the graph.

---

## Comment 10

> Username: robpieke  
> Created at: 2017-04-20 17:38:15 UTC  
> Url: https://github.com/boostorg/python/issues/103#issuecomment-295827931  

Any further thoughts/investigations on this one? Thanks in advance!

---

## Comment 11

> Username: robpieke  
> Created at: 2018-10-10 09:14:27 UTC  
> Url: https://github.com/boostorg/python/issues/103#issuecomment-428498648  

We're still patching boost locally (using the 1-line change from the original post) to address this. Any further thoughts?
