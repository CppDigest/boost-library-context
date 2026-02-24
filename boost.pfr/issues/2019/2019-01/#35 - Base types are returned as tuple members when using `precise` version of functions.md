# #35 - Base types are returned as tuple members when using `precise` version of functions. [Closed]

> Username: zmij  
> Created at: 2019-01-18 16:30:52 UTC  
> Updated at: 2020-10-04 11:49:23 UTC  
> Closed at: 2020-10-04 11:46:21 UTC  
> Url: https://github.com/boostorg/pfr/issues/35  

Here is some minimal example and output from Metashell:  
  
```C++  
using std::declval;  
using namespace boost::pfr;  
  
struct A {  
  int a;  
};  
  
// metashell interaction  
> decltype(structure_to_tuple(declval<A>()))  
std::tuple<int>  
> decltype(flat_structure_to_tuple(declval<A>()))  
std::tuple<int>  
// end metashell interaction, so far so good  
  
// An empty structure with a single base  
struct B : A {};  
  
// metashell interaction  
> decltype(structure_to_tuple(declval<B>()))  
std::tuple<A>  
> decltype(flat_structure_to_tuple(declval<B>()))  
std::tuple<int>  
// end metashell interaction  
  
struct C {  
  float c;  
};  
  
struct D : A, C {};  
// metashell interaction  
> decltype(structure_to_tuple(declval<D>()))  
std::tuple<A, C>  
> decltype(flat_structure_to_tuple(declval<D>()))  
std::tuple<int, float>  
// end metashell interaction  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2020-10-04 11:49:01 UTC  
> Updated at: 2020-10-04 11:49:23 UTC  
> Url: https://github.com/boostorg/pfr/issues/35#issuecomment-703243952  

This is an expected but very confusing behavior. Unfortunately I have no idea, how to make it more consistent. All I can do - is to highlight that behavior in docs (see a16d99b).  
  
Alas, AFAIK nothing could be improved here.  
  
Anyway, thanks for the bug report!
