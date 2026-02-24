# #73 - triviality of pair [Closed]

> Username: mglisse  
> Created at: 2018-06-08 13:41:26 UTC  
> Updated at: 2018-06-16 14:47:54 UTC  
> Closed at: 2018-06-14 11:20:14 UTC  
> Url: https://github.com/boostorg/container/issues/73  

The following hack  
```c++  
namespace boost {  
  namespace move_detail {  
    template<class A, class B> struct is_trivially_copy_assignable<boost::container::dtl::pair<A,B>> {  
      static const bool value = is_trivially_copy_assignable<A>::value && is_trivially_copy_assignable<B>::value;  
    };  
  }  
}  
```  
changes the running time of this trivial benchmark from 3.4s to just 1.0s on my laptop with `g++ -DNDEBUG -O3 -march=native`. I think it would be good if you could make pair trivial when the elements are, or at least lie convincingly through traits so an efficient implementation of algorithms is used.  
```c++  
#include <boost/container/flat_map.hpp>  
int main(int argc,char**){  
  boost::container::flat_map<int,int> m;  
  for(int n=100000;n>=0;--n)  
    m[n]=n;  
  return m[argc];  
}  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-06-14 11:20:13 UTC  
> Url: https://github.com/boostorg/container/issues/73#issuecomment-397260335  

Many thanks. Included in the commit:  
  
https://github.com/boostorg/container/commit/9a22431578339e72d0b32e1304be769da120a0de

---

## Comment 2

> Username: mglisse  
> Created at: 2018-06-14 15:06:22 UTC  
> Url: https://github.com/boostorg/container/issues/73#issuecomment-397329268  

Thanks. I thought you would want to make pair trivial, so I only listed the most important trait, but if we are going to specialize traits, it isn't the only one that is relevant.  
```c++  
template<class A, class B> struct is_trivially_copy_constructible<boost::container::dtl::pair<A,B>>  
: and_<is_trivially_copy_constructible<A>, is_trivially_copy_constructible<B> > {};  
```  
(copy vs assign)  
also gives some (smaller) gains on different testcases. I didn't test if is_trivially_default_constructible, is_trivially_move_(constructi|assigna)ble or some of the other specialized traits (is_memzero_initializable) make a difference.

---

## Comment 3

> Username: igaztanaga  
> Created at: 2018-06-16 12:47:18 UTC  
> Url: https://github.com/boostorg/container/issues/73#issuecomment-397810146  

I think "is_trivially_default_constructible" would break code as pair is expected to be value initialized. move variants and "is_trivially_destructible" seem safer. Thanks again for the additional comments. Commited in:  
  
https://github.com/boostorg/container/commit/4faa05e36c911d88dbe7b5a2cfcfd07fc6f40233

---

## Comment 4

> Username: mglisse  
> Created at: 2018-06-16 13:04:09 UTC  
> Url: https://github.com/boostorg/container/issues/73#issuecomment-397811110  

Nice, thanks for all the recent patches. Recent gcc warns (-Wclass-memaccess) about using mem* on a non-trivial type, but I just ignore it. Supposedly we could suppress the warning with some extra casts.

---

## Comment 5

> Username: igaztanaga  
> Created at: 2018-06-16 14:36:35 UTC  
> Url: https://github.com/boostorg/container/issues/73#issuecomment-397816494  

Let's see if   
  
https://github.com/boostorg/container/commit/62a8b6666eb0f12242fb1e99daa98533ce74e735  
  
helps

---

## Comment 6

> Username: mglisse  
> Created at: 2018-06-16 14:47:54 UTC  
> Url: https://github.com/boostorg/container/issues/73#issuecomment-397817143  

Looks like it does, great :+1:
