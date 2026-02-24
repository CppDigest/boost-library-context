# #173 - Consider making containers trivially copyable when possible [Open]

> Username: alfC  
> Created at: 2018-03-13 23:40:12 UTC  
> Updated at: 2018-05-05 19:20:57 UTC  
> Url: https://github.com/boostorg/fusion/issues/173  

In principle, there is no fundamental reasons that a container of `trivially_copyable` elements can not be made `trivially_copyable`. This feature can be useful for optimizing and make safe certain operations for applications that access memory directly.   
  
```  
#include<boos/fusion/container/vector.hpp>  
int main(){  
	assert((std::is_trivially_copyable<boost::fusion::vector<double, int>>::value));  
}  
```  
I understand the implementation can be tricky and `std::pair` and `std::tuple` have this "defect".   
Simple "tuples" such as `std::complex` and `std::array` do not have this problem, showing that it is possible.  
  
Some material:  
https://stackoverflow.com/questions/49120246/which-type-trait-would-indicate-that-type-is-memcpy-assignable-tuple-pair/49142680?noredirect=1#comment85373972_49142680  
  
https://groups.google.com/a/isocpp.org/forum/#!topic/std-discussion/PUZ9WUr2AOU  
  
https://groups.google.com/a/isocpp.org/forum/#!msg/std-proposals/Zi2wriFMFvI/N1JlOE-FBQAJ  
  
https://stackoverflow.com/questions/38779985/why-cant-stdtupleint-be-trivially-copyable?noredirect=1&lq=1

---

## Comment 1

> Username: djowel  
> Created at: 2018-03-14 05:47:06 UTC  
> Url: https://github.com/boostorg/fusion/issues/173#issuecomment-372911727  

Makes sense! Would you contribute a patch or a PR? ;-)

---

## Comment 2

> Username: Flast  
> Created at: 2018-03-14 06:17:21 UTC  
> Url: https://github.com/boostorg/fusion/issues/173#issuecomment-372916433  

I'm working on this issue incl. optimizing vector. It's not so hard.

---

## Comment 3

> Username: alfC  
> Created at: 2018-03-14 06:59:15 UTC  
> Url: https://github.com/boostorg/fusion/issues/173#issuecomment-372923319  

The same can be said about any `std::is_trivially_***` trait.  
  
Also, for reference, Boost.Hana doesn't seem to have this limitation:  
```  
static_assert(std::is_trivially_copyable<hana::tuple<double, int>>{});  
```

---

## Comment 4

> Username: vtnerd  
> Created at: 2018-03-20 03:31:47 UTC  
> Updated at: 2018-03-20 03:32:25 UTC  
> Url: https://github.com/boostorg/fusion/issues/173#issuecomment-374461721  

I did some initial testing on this a while back for C++11 vector. I didn't post it because I felt like there were already too many changes. Making it trivial to copy / move should also improve compile times since it is currently using a generic function with more template instantiations. Adding a conversion copy from vector also helped compile times (if implemented carefully). So there are multiple positive benefits to this ...  
  
EDIT: Too many changes -> this was when I was fixing one bug and changed the constructor SFINAE implementation for C++11 vector.

---

## Comment 5

> Username: Flast  
> Created at: 2018-05-02 23:58:22 UTC  
> Updated at: 2018-05-03 03:05:46 UTC  
> Url: https://github.com/boostorg/fusion/issues/173#issuecomment-386156650  

I have a concern what I want to confirm, following code is (or will be) valid and expected, right?  
  
```cpp  
static_assert(std::is_trivially_copyable<fusion::vector<int&>>{}); // OK  
// because the struct is trivially copyable even if contains reference  
struct S  
{  
    int& r;  
};  
static_assert(std::is_trivially_copyable<S>{}); // also OK  
```

---

## Comment 6

> Username: alfC  
> Created at: 2018-05-03 00:22:53 UTC  
> Updated at: 2018-05-03 00:31:23 UTC  
> Url: https://github.com/boostorg/fusion/issues/173#issuecomment-386160549  

On Wed, May 2, 2018 at 4:58 PM, Kohei Takahashi <notifications@github.com>  
wrote:  
>  
> I have an concern what I want to confirm, following code is (or will be)  
>  
> static_assert(std::is_trivially_copyable<fusion::vector<int&>>{}); // OK  
>  
> // because the struct is trivially copyable even if contains reference  
> struct S  
> {  
>     int& r;  
> };  
> static_assert(std::is_trivially_copyable<S>{}); // also OK  
>  
  
Good point,  
  
First, is it even technical possible? the internal reference would need to  
be initialized or assigned explicitly somehow.  
  
I think the answer should be no (bfn::vector<int&> should not be trivially  
copyable).  
Although there are confusing signals.  
  
On one hand:  
  
std::is_trivially_copyable<fusion::vector<int >> should be true because  
std::is_trivially_copyable<int > is true.  
std::is_trivially_copyable<fusion::vector<int&>> should be false because  
std::is_trivially_copyable<int&> is false.  
  
On the other hand, this is then inconsistent:  
  
std::is_trivially_copyable<fusion::vector<int&>> should be true because  
std::is_trivially_copyable<S> is true.  
(your point)  
  
But I think this points to an anomaly in the language which is that a  
structure of references should forward the assignment element by element by  
default, even if (some) members are references.  
(In other words, how struct S can be trivially copyable when a member is  
not.)  
Perhaps the anomaly is that int& should be trivially_copyable... precisely  
because it is not copyable! I don't know.  
  
Another point is, does boost::fusion::vector<...> strictly models a struct?,  
and if so, do we want to model the anomaly with it?.  
(My preferred answer as of today is "no", sometimes I use bfn::vector or std::tuple,  
precisely to avoid the above anomaly of C++ structs, including recursive  
assignment, and recursive comparison, which I wish they were in the language by default)  
  
There is this talk by Nicole Mazzuca which is kind of messy but it raises some  
interesting questions related to this https://www.youtube.com/watch?v=uYDt1gCDxhM

---

## Comment 7

> Username: alfC  
> Created at: 2018-05-03 01:04:16 UTC  
> Url: https://github.com/boostorg/fusion/issues/173#issuecomment-386166269  

There is a more utilitarian point of view.  
  
In practical context...  
Trivially copyable means "s1 = s2 has the same effect as std::memcpy(&s1,  
&s2, sizeof(S))"  
  
For `struct S` the `s1 = s2` doesn't have a meaning in the first place  
(doesn't compile),  
so what would it mean?  
  
  
  
  
On Wed, May 2, 2018 at 5:22 PM, Alfredo Correa <alfredo.correa@gmail.com>  
wrote:  
  
>  
>  
> On Wed, May 2, 2018 at 4:58 PM, Kohei Takahashi <notifications@github.com>  
> wrote:  
> >  
> > I have an concern what I want to confirm, following code is (or will be)  
> valid and expected, right?  
> >  
> > static_assert(std::is_trivially_copyable<fusion::vector<int&>>{}); // OK  
> >  
> > // because the struct is trivially copyable even if contains reference  
> > struct S  
> > {  
> >     int& r;  
> > };  
> > static_assert(std::is_trivially_copyable<S>{}); // also OK  
> >  
>  
> Good point,  
>  
> First, is it even technical possible? the internal reference would need to  
> be initialized or assigned explicitly somehow.  
>  
> I think the answer should be no (bfn::vector<int&> should not be trivially  
> copyable).  
> Although it there are confusing signals.  
>  
> On one hand:  
>  
> std::is_trivially_copyable<fusion::vector<int >> should be true because  
> std::is_trivially_copyable<int > is true.  
> std::is_trivially_copyable<fusion::vector<int&>> should be false because  
> std::is_trivially_copyable<int&> is false.  
>  
> but then this is inconsistent:  
> std::is_trivially_copyable<fusion::vector<int&>> should be true because  
> std::is_trivially_copyable<S> is true.  
> (your point)  
>  
> But I think this points to an anomaly in the language that is that a  
> structure of references should forward the assignment element by element by  
> default, even if (some) members are references.  
> (In other words, how struct S can be trivially copyable when a member is  
> not.)  
> Perhaps the anomaly is that int& should be trivially_copyable... precisely  
> because it is not copyable! I don't know.  
>  
> Another point is, does boost::fusion::vector<...> strictly models a  
> struct, and if so, do we want to model the anomaly with it.  
> (My answer as of today is "no", sometimes I use bfn::vector or std::tuple,  
> precisely to avoid the above anomaly of C++ structs, including recursive  
> assignment, and recursive comparison)  
>  
> There is this talk by Nicole Mazzuca which kind of messy but it raises  
> some interesting questions related to this https://www.youtube.com/watch?  
> v=uYDt1gCDxhM  
>  
>  
  
  
--   
Alfredo

---

## Comment 8

> Username: Flast  
> Created at: 2018-05-03 03:26:32 UTC  
> Url: https://github.com/boostorg/fusion/issues/173#issuecomment-386182783  

@alfC thanks.  
  
> My answer as of today is "no", sometimes I use bfn::vector or std::tuple, precisely to avoid the above anomaly of C++ structs, including recursive assignment, and recursive comparison  
  
That usage was my concern. I thought it is natural to me that `tuple<int&>` is trivially copyable because basically I use tuple as just unnamed struct.  
  
Unfortunately, In my research, making`vector<int&>` non trivially copyable is incompatible with trivially copyable vector....

---

## Comment 9

> Username: alfC  
> Created at: 2018-05-03 06:51:01 UTC  
> Url: https://github.com/boostorg/fusion/issues/173#issuecomment-386205234  

On Wed, May 2, 2018 at 8:26 PM, Kohei Takahashi <notifications@github.com>  
wrote:  
  
> @alfC <https://github.com/alfC> thanks.  
>  
> My answer as of today is "no", sometimes I use bfn::vector or std::tuple,  
> precisely to avoid the above anomaly of C++ structs, including recursive  
> assignment, and recursive comparison  
>  
> That usage was my concern. I thought it is natural to me that tuple<int&>  
> is trivially copyable because basically I use tuple as just unnamed struct.  
>  
I think it is a good use, but also notice that it doesn't behave as a pure  
struct since operator= for tuple works as expected. And that is in my  
opinion) why it is a good thing.  
  
  
> Unfortunately, In my research, makingvector<int&> non trivially copyable  
> is incompatible with trivially copyable vector....  
>  
I think since int& is not trivially copyable, then, for consistency alone  
vector<int&> shouldn't be either.  
Whether it is a good idea that int& is not trivially copyable is a  
different discussion.  
  
What do you mean that is "incompatible"? The way I see it is that  
unfortunately vector<int&> and vector<int> would have to be implemented  
differently because they will have different implementations of operator=.  
  
IF the language didn't have the quirk it has one would be able to implement  
a tuple that is trivially copyable very easily by just saying.  
`template<class T1, class... Ts> tuple { T1 t1; tuple<Ts...> rest; }`, and  
operator= will work as expected for tuple<T&, ...> and it would be  
trivially copiable if T1 is trivially copyable and recursively tuple<Ts...>  
is trivially copyable.  
  
The current state of the language forces to either  
1) force an explicit `operator=` and therefore making tuple not trivially  
copyable in cases where it should. (this is the current implementation I  
think).  
2) force an explicit `operator=` only for certain specializations (those  
involving references at some level).  
  
I though that 2) would be the route to solve this specific issue. Is that  
the route you are taking?  
By the way, Boost.Hana, either by accident or by design doesn't have this  
issue. Hana containers that are expected to be trivially copyable, are  
trivially copyable. Maybe worth looking.

---

## Comment 10

> Username: alfC  
> Created at: 2018-05-03 08:16:36 UTC  
> Updated at: 2018-05-03 08:46:03 UTC  
> Url: https://github.com/boostorg/fusion/issues/173#issuecomment-386221933  

Looks like in C++11 is not that difficult to solve this issue. It seems to be simply necessary to not explicitly define the `operator=(tuple const&)`.  
  
The *natural* result for tuples containing references is very strange. (Is it desirable?)   
  
```  
#include<utility>  
#include<type_traits>  
  
template<class T1, class... Ts>  
struct tpl{  
   T1 head_;  
   tpl<Ts...> rest_;  
/*   tpl& operator=(tpl const& o){  
       head_ = o.head_;  
       rest_ = o.rest_;  
       return *this;  
   }*/  
   template<class Other> tpl& operator=(Other&& o){  
       head_ = std::forward<Other>(o).head_;  
       rest_ = std::forward<Other>(o).rest_;  
       return *this;  
   }  
};  
template<class T>  
struct tpl<T>{  
   T t_;  
/*   tpl& operator=(tpl const& o){  
       t_ = o.t_;  
   }*/  
   template<class Other> tpl& operator=(Other&& o){  
       t_ = std::forward<Other>(o).t_;  
       return *this;  
   }  
};  
int main()  
{  
    static_assert(std::is_trivially_copyable<tpl<int, double>>{});  
    tpl<int, double> t1 = {};  
    tpl<int, double> t2 = {};  
    t2 = t1;  
    int i1 = {};  
    double d1 = {};  
    tpl<int&, double&> tr1{i1, d1};  
    tpl<int&, double&> tr2{i1, d1};  
    tr1 = t1; // fails unless the specializations in line 15  
    tr2 = tr1;  
    t2 = tr1;  
   // this combination of results is strange  
    static_assert(!std::is_trivially_copyable<tpl<int&, double&>>{});  
    static_assert(std::is_trivially_copyable<tpl<int, double&>>{});   
    static_assert(std::is_trivially_copyable<tpl<int&, double>>{});  
}  
```

---

## Comment 11

> Username: Flast  
> Created at: 2018-05-05 11:04:58 UTC  
> Url: https://github.com/boostorg/fusion/issues/173#issuecomment-386797597  

> What do you mean that is "incompatible"? The way I see it is that unfortunately vector<int&> and vector<int> would have to be implemented differently because they will have different implementations of operator=.  
  
I meant, `vector<int&>` requires user-defined special functions to be not trivially copyable, but `vector<int>` should not have any user-defined special functions to be trivially copyable.  
It can't be solved using SFINAE, because compiler generated special functions (i.e. non-templated) is viable than templated user-defined special functions (n4687 16.3.3 [over.match.best] /1 (1-6)).  
  
> IF the language didn't have the quirk it has one would be able to implement a tuple that is trivially copyable very easily by just saying. `template<class T1, class... Ts> tuple { T1 t1; tuple<Ts...> rest; }`, and operator= will work as expected for tuple<T&, ...> and it would be trivially copiable if T1 is trivially copyable and recursively tuple<Ts...> is trivially copyable.  
  
I'm against this way, recursive may be costlier than trivially copyable.  
  
I've got it! It might be solved using helper base class, like this.  
  
```cpp  
struct non_trivial  
{  
    non_trivial& operator=(non_trivial const&) { return *this; }  
};  
  
struct trivial  
{  
};  
  
template <typename T, typename U>  
struct tpl : std::conditional_t<std::is_trivially_copyable<T>{} && std::is_trivially_copyable<U>{}, trivial, non_trivial>  
```  
https://wandbox.org/permlink/k7xnlZDDAubWSI16  
  
It may not break current optimal (non recursive) implementation.  
  
  
OFF TOPIC:  
```cpp  
   // this combination of results is strange  
    static_assert(!std::is_trivially_copyable<tpl<int&, double&>>{});  
    static_assert(std::is_trivially_copyable<tpl<int, double&>>{});   
    static_assert(std::is_trivially_copyable<tpl<int&, double>>{});  
```  
GCC passes all, but Clang fails on second. I think Clang is correct.  
Compiler generating assign of `tpl<T>` is implicitly deleted (same reason as struct), and then compiler generating assign of `tpl<T1, Ts...>` instantiates user defined assign (i.e. breaks trivially copyable requirements).

---

## Comment 12

> Username: alfC  
> Created at: 2018-05-05 19:20:57 UTC  
> Url: https://github.com/boostorg/fusion/issues/173#issuecomment-386828691  

Yes, I agree, although there maybe more natural ways to obtain this result. The other problem is that for the non-trivial case, *something* needs to be done to    `operator=(tpl const& other)`.  Not sure if you are looking for a solution in the space of C++11, but I found in comment https://github.com/boostorg/fusion/issues/173#issuecomment-386221933 that it is possilble to have a very compact implementation that respects the trivially copyable requirement.
