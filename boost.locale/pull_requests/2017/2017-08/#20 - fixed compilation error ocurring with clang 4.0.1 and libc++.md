# #20 fixed compilation error ocurring with clang 4.0.1 and libc++ [Closed]

> Username: adrianimboden  
> Created at: 2017-08-03 14:35:50 UTC  
> Updated at: 2017-08-07 16:24:44 UTC  
> Closed at: 2017-08-07 13:44:42 UTC  
> Url: https://github.com/boostorg/locale/pull/20  

I'm building the boost library myself and am now newly using boost.locale.  
I'm using clang 4.0.1 with the llvm C++ standard library libc++ and using the -std=c++1z compiler flag. With that combination the following error occurs:  
`libs/locale/src/icu/boundary.cpp:137:39: error: no viable conversion from 'auto_ptr<...>' to 'auto_ptr<...>'`  
  
Complete error message:  
[error.txt](https://github.com/boostorg/locale/files/1197507/error.txt)

---

## Comment 1

> Username: artyom-beilis  
> Created_at: 2017-08-03 15:43:21 UTC  
> Url: https://github.com/boostorg/locale/pull/20#issuecomment-320008189  

Question... I don't really understand why it works. auto_ptr does not have move constructor why std::move works with one?

---

## Comment 2

> Username: adrianimboden  
> Created_at: 2017-08-03 19:16:02 UTC  
> Url: https://github.com/boostorg/locale/pull/20#issuecomment-320063962  

It has something to do with a difference in the C++17 standard.  
  
I made the following observation:  
This produces the given error  
`std::auto_ptr<icu::BreakIterator> bi(get_iterator(t,loc));`  
while these all do work:  
`std::auto_ptr<icu::BreakIterator> bi{get_iterator(t,loc))};`  
`std::auto_ptr<icu::BreakIterator> bi = get_iterator(t,loc));`  
`auto copy = get_iterator(t,loc); std::auto_ptr<icu::BreakIterator> it(copy);`  
  
This is how the auto_ptr implementation looks like:  
  
    class auto_ptr  
    {  
    public:  
        typedef X element_type;  
  
        explicit auto_ptr(X* p =0) throw();  
        auto_ptr(auto_ptr&) throw();  
        template<class Y> auto_ptr(auto_ptr<Y>&) throw();  
        auto_ptr& operator=(auto_ptr&) throw();  
        template<class Y> auto_ptr& operator=(auto_ptr<Y>&) throw();  
        auto_ptr& operator=(auto_ptr_ref<X> r) throw();  
        ~auto_ptr() throw();  
  
        typename add_lvalue_reference<X>::type operator*() const throw();  
        X* operator->() const throw();  
        X* get() const throw();  
        X* release() throw();  
        void reset(X* p =0) throw();  
  
        auto_ptr(auto_ptr_ref<X>) throw();  
        template<class Y> operator auto_ptr_ref<Y>() throw();  
        template<class Y> operator auto_ptr<Y>() throw();  
    };  
  
It does not make much sense to me, why this should not work anymore tough.

---

## Comment 3

> Username: artyom-beilis  
> Created_at: 2017-08-03 19:50:08 UTC  
> Url: https://github.com/boostorg/locale/pull/20#issuecomment-320071919  

It seems to be some wired bug in clang/libc++.  
  
Actually `auto_ptr` was removed in C++17 (horrible thing :-( )  
  
If this `std::auto_ptr<icu::BreakIterator> bi = get_iterator(t,loc)` works - it should be ok to fix it this way since it is C++2003 compliant.  
  
I need to address this problem in general. To provide some build for Boost.Locale without auto_ptr in mode >= C++11

---

## Comment 4

> Username: adrianimboden  
> Created_at: 2017-08-04 07:58:40 UTC  
> Url: https://github.com/boostorg/locale/pull/20#issuecomment-320183988  

Yeah, the auto_ptr removal will result in quite some work, especially if we want to support C++03.

---

## Comment 5

> Username: mclow  
> Created_at: 2017-08-07 01:02:17 UTC  
> Url: https://github.com/boostorg/locale/pull/20#issuecomment-320545102  

I just want to point out that this will become important fairly quickly, because both some version of MSVC 2017 and Clang 5.0 have removed `auto_ptr` when compiling for C++17 and later.  I don't know the MS release schedule, but Clang 5.0 will be released in September (next month; we just built RC1)

---

## Comment 6

> Username: artyom-beilis  
> Created_at: 2017-08-07 10:37:51 UTC  
> Url: https://github.com/boostorg/locale/pull/20#issuecomment-320630194  

Note I added support of unique_ptr and removed it internally. See https://github.com/boostorg/locale/commit/322437a485c63d9fce4dc620597b6c75b6396daf  
  
Can you test develop branch if it fixes the issues?

---

## Comment 7

> Username: mclow  
> Created_at: 2017-08-07 14:05:54 UTC  
> Url: https://github.com/boostorg/locale/pull/20#issuecomment-320673034  

I built the `develop` branch this morning on trunk libc++ with -std=c++1z and it compiled and ran the tests successfully.

---

## Comment 8

> Username: artyom-beilis  
> Created_at: 2017-08-07 14:14:14 UTC  
> Url: https://github.com/boostorg/locale/pull/20#issuecomment-320675205  

With my latest updates? i.e.   322437a

---

## Comment 9

> Username: mclow  
> Created_at: 2017-08-07 14:19:28 UTC  
> Url: https://github.com/boostorg/locale/pull/20#issuecomment-320676610  

Yes.

---

## Comment 10

> Username: adrianimboden  
> Created_at: 2017-08-07 16:24:44 UTC  
> Url: https://github.com/boostorg/locale/pull/20#issuecomment-320711394  

Ah perfect. Even better.

---
