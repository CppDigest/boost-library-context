# #170 - Boost\libs\type_traits failed to build due to error C2338 on MSVC [Closed]

> Username: QuellaZhang  
> Created at: 2022-06-01 07:08:47 UTC  
> Updated at: 2022-06-20 02:54:03 UTC  
> Closed at: 2022-06-01 07:23:36 UTC  
> Url: https://github.com/boostorg/type_traits/issues/170  

**Issue description:**  
Boost\libs\type_traits failed to build due to error C2338 on MSVC. Could you please take a look? We ues https://github.com/boostorg/boost/commit/8bfc57e268c536f68d3832741bccdefad534d9d4 from Boost master branch.  
  
**Build step:**  
  
1. git clone -c core.autocrlf=true --recursive ​https://github.com/boostorg/boost.git boost  
2. open a VS 2019 x86 command prompt and browse to boost  
3. .\bootstrap  
4. .\b2 headers variant=release --build-dir=..\out\x86rel address-model=86  
5. .\b2 variant=release --build-dir=..\out\x86rel address-model=86  
6. .\b2 -j16 variant=release --build-dir=..\out\x86rel libs\type_traits\test  
  
**Error info:**  
promote_enum_msvc_bug_test.cpp  
libs\type_traits\test\promote_enum_msvc_bug_test.cpp(32): error C2338: (boost::is_same<T, unsigned int>::value)  
libs\type_traits\test\promote_enum_msvc_bug_test.cpp(37): note: see reference to function template instantiation 'void assert_is_uint<int>(T)' being compiled  
        with  
        [  
            T=int  
        ]  
F:\gitP\boostorg\boost\libs\type_traits\test\promote_util.hpp(30): error C2338: ( ::boost::is_same< promoted , Promoted >::value )  
libs\type_traits\test\promote_enum_msvc_bug_test.cpp(38): note: see reference to function template instantiation 'void test_cv<UIntEnum,unsigned int>(void)' being compiled  
F:\gitP\boostorg\boost\libs\type_traits\test\promote_util.hpp(31): error C2338: ( ::boost::is_same< promoted_c , Promoted const >::value )  
F:\gitP\boostorg\boost\libs\type_traits\test\promote_util.hpp(32): error C2338: ( ::boost::is_same< promoted_v , Promoted volatile >::value )  
F:\gitP\boostorg\boost\libs\type_traits\test\promote_util.hpp(33): error C2338: ( ::boost::is_same< promoted_cv, Promoted const volatile >::value )

---

## Comment 1

> Username: jzmaddock  
> Created at: 2022-06-01 07:23:36 UTC  
> Url: https://github.com/boostorg/type_traits/issues/170#issuecomment-1143211565  

The clue is in the name of the test :)  
  
MSVC still does not implement correct enum promotions, so this is an expected failure.

---

## Comment 2

> Username: QuellaZhang  
> Created at: 2022-06-06 10:13:38 UTC  
> Updated at: 2022-06-20 02:54:03 UTC  
> Url: https://github.com/boostorg/type_traits/issues/170#issuecomment-1147286385  

@jzmaddock Thanks for the clarification, I have confirmed this is a compiler bug. Can you help confirm the following 2 issues?  
  
### **issue1:**  
is_nothrow_move_constructible_test.cpp  
.\boost/type_traits/is_nothrow_move_constructible.hpp(49): error C2248: 'non_copy::non_copy': cannot access private member declared in class 'non_copy'  
libs\type_traits\test\is_nothrow_move_constructible_test.cpp(24): note: see declaration of 'non_copy::non_copy'  
libs\type_traits\test\is_nothrow_move_constructible_test.cpp(20): note: see declaration of 'non_copy'  
.\boost/type_traits/is_nothrow_move_constructible.hpp(55): note: see reference to class template instantiation 'boost::detail::false_or_cpp11_noexcept_move_constructible<T,void>' being compiled  
        with  
        [  
            T=non_copy  
        ]  
libs\type_traits\test\is_nothrow_move_constructible_test.cpp(256): note: see reference to class template instantiation 'boost::is_nothrow_move_constructible<non_copy>' being compiled  
.\boost/type_traits/is_nothrow_move_constructible.hpp(49): error C2280: 'delete_copy::delete_copy(const delete_copy &)': attempting to reference a deleted function  
libs\type_traits\test\is_nothrow_move_constructible_test.cpp(32): note: see declaration of 'delete_copy::delete_copy'  
libs\type_traits\test\is_nothrow_move_constructible_test.cpp(32): note: 'delete_copy::delete_copy(const delete_copy &)': function was explicitly deleted  
.\boost/type_traits/is_nothrow_move_constructible.hpp(55): note: see reference to class template instantiation 'boost::detail::false_or_cpp11_noexcept_move_constructible<T,void>' being compiled  
        with  
        [  
            T=delete_copy  
        ]  
libs\type_traits\test\is_nothrow_move_constructible_test.cpp(260): note: see reference to class template instantiation 'boost::is_nothrow_move_constructible<delete_copy>' being compiled  
  
### **issue2:**  
is_nothrow_move_assignable_test.cpp  
.\boost/type_traits/is_nothrow_move_assignable.hpp(52): error C2280: 'non_assignable &non_assignable::operator =(const non_assignable &)': attempting to reference a deleted function  
libs\type_traits\test\is_nothrow_move_assignable_test.cpp(25): note: see declaration of 'non_assignable::operator ='  
libs\type_traits\test\is_nothrow_move_assignable_test.cpp(25): note: 'non_assignable &non_assignable::operator =(const non_assignable &)': function was explicitly deleted  
.\boost/type_traits/is_nothrow_move_assignable.hpp(58): note: see reference to class template instantiation 'boost::detail::false_or_cpp11_noexcept_move_assignable<T,void>' being compiled  
        with  
        [  
            T=non_assignable  
        ]  
libs\type_traits\test\is_nothrow_move_assignable_test.cpp(251): note: see reference to class template instantiation 'boost::is_nothrow_move_assignable<non_assignable>' being compiled
