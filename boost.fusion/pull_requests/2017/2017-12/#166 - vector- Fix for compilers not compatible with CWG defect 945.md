# #166 vector: Fix for compilers not compatible with CWG defect 945 [Merged]

> Username: Kojoley  
> Created at: 2017-12-23 15:24:03 UTC  
> Updated at: 2018-08-30 23:53:43 UTC  
> Merged at: 2017-12-24 02:40:58 UTC  
> Closed at: 2017-12-24 02:40:58 UTC  
> Url: https://github.com/boostorg/fusion/pull/166  

Closes #160 (I believe).  
Reject the PR if you are not going to add support for compilers that do not support [CWG defect 945](http://www.open-std.org/jtc1/sc22/wg21/docs/cwg_defects.html#945) (c++11 in clang 3.0, gcc 4.6?). Also, I am not sure this is enough and there are no other problems with them.

---

## Comment 1

> Username: djowel  
> Created_at: 2017-12-23 19:51:02 UTC  
> Url: https://github.com/boostorg/fusion/pull/166#issuecomment-353744764  

I'm not sure if we should support such compilers. However, this is such an easy workaround that it's probably worth having. I'm on the fence. I'll leave it up to @Flast to decide.

---

## Comment 2

> Username: Flast  
> Created_at: 2017-12-24 02:38:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/166#issuecomment-353761708  

> this is such an easy workaround that it's probably worth having  
  
Yes, it's trivial. I have no reason for rejecting this.

---

## Comment 3

> Username: Flast  
> Created_at: 2017-12-24 02:40:51 UTC  
> Url: https://github.com/boostorg/fusion/pull/166#issuecomment-353761772  

Ah, it's assigned to me. OK, I'll merge.

---

## Comment 4

> Username: Flast  
> Created_at: 2017-12-24 02:41:07 UTC  
> Url: https://github.com/boostorg/fusion/pull/166#issuecomment-353761782  

Thank you, both!

---

## Comment 5

> Username: djowel  
> Created_at: 2017-12-24 10:09:43 UTC  
> Url: https://github.com/boostorg/fusion/pull/166#issuecomment-353776230  

> Thank you, both!  
  
It's always good to know Spirit, Fusion and Phoenix are in very capable hands.

---

## Comment 6

> Username: prudhomm  
> Created_at: 2018-08-18 22:47:40 UTC  
> Url: https://github.com/boostorg/fusion/pull/166#issuecomment-414090817  

@flast I don't think it is such as simple change. It breaks my c++14 code and reverting back to the previous one fixes it.

---

## Comment 7

> Username: prudhomm  
> Created_at: 2018-08-18 22:55:03 UTC  
> Url: https://github.com/boostorg/fusion/pull/166#issuecomment-414091089  

@Kojoley @djowel @Flast I get this kind of errors:  
```  
/home/feelpp/feelpp/feel/feeldiscr/functionspace.hpp:5358:31: error: field has incomplete type 'Feel::FunctionSpace<Feel::Mesh<Simplex<3, 2, 3> >,  
      Feel::detail::bases<Feel::Lagrange<1, Scalar, Feel::Continuous, PointSetEquiSpaced, 0> >, double, Feel::Periodicity<Feel::NoPeriodicity>, Feel::mortars<Feel::NoMortar>  
      >::functionspace_vector_type' (aka 'vector<boost::shared_ptr<Feel::FunctionSpace<Feel::Mesh<Simplex<3, 2, 3> >, Feel::detail::bases<Feel::Lagrange<1, Scalar,  
      Feel::Continuous, PointSetEquiSpaced, 0> >, double, Feel::Periodicity<Feel::NoPeriodicity>, Feel::mortars<Feel::NoMortar> > > >')  
    functionspace_vector_type M_functionspaces;  
                              ^  
/usr/include/boost/fusion/container/vector/vector.hpp:250:61: note: in instantiation of template class 'Feel::FunctionSpace<Feel::Mesh<Simplex<3, 2, 3> >,  
      Feel::detail::bases<Feel::Lagrange<1, Scalar, Feel::Continuous, PointSetEquiSpaced, 0> >, double, Feel::Periodicity<Feel::NoPeriodicity>, Feel::mortars<Feel::NoMortar> >'  
      requested here  
            auto at_impl(J) -> decltype(at_detail<J::value>(&std::declval<vector_data&>()))                                                      ^  
```  
This is open-source code [here](https://github.com/feelpp/feelpp) and I can produce the relevant code.  
it also produces some weird error messages at some simple mpl/fusion code like  
```  
/usr/include/boost/mpl/fold.hpp:34:44: error: no member named 'value' in 'boost::mpl::O1_size<boost::fusion::vector<boost::shared_ptr<Feel::FunctionSpace<Feel::Mesh<Hypercube<3,  
      1, 3> >, Feel::detail::bases<Feel::Lagrange<1, Scalar, Feel::Continuous, PointSetEquiSpaced, 0> >, double, Feel::Periodicity<Feel::NoPeriodicity>,  
      Feel::mortars<Feel::NoMortar> > > > >'  
          ::boost::mpl::O1_size<Sequence>::value  
          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~^  
```  
the c++ code looks like   
```  
typedef typename mpl::transform<functionspace_vector_type, ChangeMeshSupport<mpl::_1>, mpl::back_inserter<fusion::vector<> > >::type mesh_support_vector_type;  
```  
where `ChangeMeshSupport` is a very simple class.  
I was looking initially into this, but the culprit was the change you committed and which produced the first error I reported

---

## Comment 8

> Username: prudhomm  
> Created_at: 2018-08-18 22:56:34 UTC  
> Url: https://github.com/boostorg/fusion/pull/166#issuecomment-414091141  

so basically the code cannot use boost starting from 1.67 due to this since it relies heavily on fusion

---

## Comment 9

> Username: Kojoley  
> Created_at: 2018-08-18 23:01:01 UTC  
> Url: https://github.com/boostorg/fusion/pull/166#issuecomment-414091319  

It is very strange, I do not see any problems in [Regression](https://www.boost.org/development/tests/develop/developer/fusion.html). What is your compiler? Do you have a MWE?

---

## Comment 10

> Username: prudhomm  
> Created_at: 2018-08-18 23:02:59 UTC  
> Url: https://github.com/boostorg/fusion/pull/166#issuecomment-414091392  

clang >= 4

---

## Comment 11

> Username: prudhomm  
> Created_at: 2018-08-18 23:14:18 UTC  
> Updated_at: 2018-08-18 23:14:25 UTC  
> Url: https://github.com/boostorg/fusion/pull/166#issuecomment-414091873  

@Kojoley could it be possible to have boost macros to change the PR and detect the supported vs unsupported compilers and select the corresponding implementation ?

---

## Comment 12

> Username: Kojoley  
> Created_at: 2018-08-18 23:23:07 UTC  
> Url: https://github.com/boostorg/fusion/pull/166#issuecomment-414092238  

I am fine with reverting the PR (since it targeted somewhat outdated compilers), however I do not understand how it broke.  
Do you inherit from fusion types and may be overload address-of operator?

---

## Comment 13

> Username: prudhomm  
> Created_at: 2018-08-18 23:25:17 UTC  
> Updated_at: 2018-08-18 23:25:57 UTC  
> Url: https://github.com/boostorg/fusion/pull/166#issuecomment-414092334  

indeed I sometimes have class inheriting from fusion types

---

## Comment 14

> Username: Flast  
> Created_at: 2018-08-30 23:53:42 UTC  
> Url: https://github.com/boostorg/fusion/pull/166#issuecomment-417504691  

Sorry for late reply.  
  
@prudhomm can you show me reproducible code (minimal is better but not required)?

---
