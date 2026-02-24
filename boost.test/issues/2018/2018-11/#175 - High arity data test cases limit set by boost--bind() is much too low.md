# #175 - High arity data test cases limit set by boost::bind() is much too low. [Closed]

> Username: iolojz  
> Created at: 2018-11-02 10:33:06 UTC  
> Updated at: 2018-11-15 17:13:45 UTC  
> Closed at: 2018-11-15 13:52:28 UTC  
> Url: https://github.com/boostorg/test/issues/175  

When having models with a large set of input parameters, one cannot use BOOST_DATA_TEST_CASE(). e.g in my case I need 12 input parameters.  
  
It may be reasonable to not support binding of more than 9 arguments. But not supporting test cases with ten input parameters is not. One could e.g offer something like BOOST_DATA_TUPLE_TEST_CASE() that stuffs all arguments into a tuple and supplies that to the user.

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-11-02 19:07:42 UTC  
> Updated at: 2018-11-02 19:18:15 UTC  
> Url: https://github.com/boostorg/test/issues/175#issuecomment-435479132  

Do you have a C++11 capable compiler? For C++11 compilers, you should not be impacted by this limit I think: https://www.boost.org/doc/libs/1_68_0/libs/test/doc/html/boost_test/tests_organization/test_cases/test_case_generation/datasets_auto_registration.html

---

## Comment 2

> Username: iolojz  
> Created at: 2018-11-06 08:52:16 UTC  
> Url: https://github.com/boostorg/test/issues/175#issuecomment-436176140  

Yes, I do, but that is not the issue. From `test/data/test_case.hpp:162`...  
  
    #if !defined(BOOST_TEST_DATASET_VARIADIC)  
    // see BOOST_TEST_DATASET_MAX_ARITY to increase the default supported arity  
    #define TC_MAKE(z,arity,_)                                                              \  
    template<BOOST_PP_ENUM_PARAMS(arity, typename Arg)>                                 \  
    void    operator()( BOOST_PP_ENUM_BINARY_PARAMS(arity, Arg, const& arg) ) const     \  
    {                                                                                   \  
        m_test_cases.push_back( new test_case( genTestCaseName(), m_tc_file, m_tc_line, \  
           boost::bind( &TestCase::template test_method<BOOST_PP_ENUM_PARAMS(arity,Arg)>,\  
           BOOST_PP_ENUM_PARAMS(arity, arg) ) ) );                                      \  
    }                                                                                   \  
  
    BOOST_PP_REPEAT_FROM_TO(1, BOOST_TEST_DATASET_MAX_ARITY, TC_MAKE, _)  
    #else  
    template<typename ...Arg>  
    void    operator()(Arg&& ... arg) const  
    {  
        m_test_cases.push_back(  
            new test_case( genTestCaseName(),  
                           m_tc_file,  
                           m_tc_line,  
                           boost::bind( &TestCase::template test_method<Arg...>,  
                                        boost_bind_rvalue_holder_helper(std::forward<Arg>(arg))...)));  
    }  
    #endif  
  
The culprit is `boost::bind()` which is limited to nine parameters according to https://www.boost.org/doc/libs/1_68_0/libs/bind/doc/html/bind.html#bind.implementation.number_of_arguments.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2018-11-06 13:59:29 UTC  
> Url: https://github.com/boostorg/test/issues/175#issuecomment-436261809  

Oh, right. There was a good reason for this: the forwarding of arguments was not working the same way for `boost::bind` and `std::bind` at the time I looked into this.  
I'll have a look again.

---

## Comment 4

> Username: raffienficiaud  
> Created at: 2018-11-11 23:32:43 UTC  
> Updated at: 2018-11-11 23:33:09 UTC  
> Url: https://github.com/boostorg/test/issues/175#issuecomment-437715887  

Good news, this has been fixed in boost 1.67. Would you please check again?

---

## Comment 5

> Username: iolojz  
> Created at: 2018-11-15 13:07:00 UTC  
> Url: https://github.com/boostorg/test/issues/175#issuecomment-439033801  

Indeed... My bad. I just checked it for thirty parameters and it works just fine. I am sorry, I had just blindly assumed I already had the latest version.

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2018-11-15 17:13:45 UTC  
> Url: https://github.com/boostorg/test/issues/175#issuecomment-439117462  

No worries and thanks for the feedback!
