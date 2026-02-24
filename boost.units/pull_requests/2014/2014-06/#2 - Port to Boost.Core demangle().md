# #2 Port to Boost.Core demangle() [Merged]

> Username: Lastique  
> Created at: 2014-06-11 18:40:12 UTC  
> Updated at: 2014-09-16 14:25:57 UTC  
> Merged at: 2014-09-16 14:06:15 UTC  
> Closed at: 2014-09-16 14:06:15 UTC  
> Url: https://github.com/boostorg/units/pull/2  

The change does several things:  
- Makes use of the common demangle() implementation in Boost.Core.  
- Fixes a memory leak if std::string constructor throws.  
- Removes the dependency on Boost.Algorithm.  
- Makes the behavior consistent for platforms with and without __cxa_demangle().

---

## Comment 1

> Username: jhunold  
> Created_at: 2014-09-16 07:10:58 UTC  
> Url: https://github.com/boostorg/units/pull/2#issuecomment-55704441  

Looks fine. But there is a "inline std::string demangle(const char\* name)" in "example/systems.cpp" which you might want to remove, too.  
And I did not find a test for "typename_fmt = 3,       /// output demangled typenames (useful only for diagnosis)." for which demangle is used. Can you solve both issues? Otherwise I have do to things myself which will take some time at the moment.

---

## Comment 2

> Username: Lastique  
> Created_at: 2014-09-16 07:37:59 UTC  
> Url: https://github.com/boostorg/units/pull/2#issuecomment-55706839  

I can remove demangle() from the example no problem, but I'm not sure what you mean with the typename_fmt remark. I did not add or remove any tests, and I don't think I can write one since I don't know what typename_fmt is.

---

## Comment 3

> Username: jhunold  
> Created_at: 2014-09-16 07:55:26 UTC  
> Url: https://github.com/boostorg/units/pull/2#issuecomment-55708330  

Take a look at the implementation of "operator<<" in "io.hpp" (lines 1009ff). The "typename_fmt" format flags is used to trigger the demangled output of the type when using operator<<.   
And I did not find a test for this. So I can't verify if your changes really work.

---

## Comment 4

> Username: Lastique  
> Created_at: 2014-09-16 08:38:17 UTC  
> Url: https://github.com/boostorg/units/pull/2#issuecomment-55712292  

So that basically means testing the output result? I'm afraid such test would not be portable since the output can be different depending on the platform. I can add a pseudo-test that will just output the result to console but never fail - basically, that's what we do in Boost.Core.  
  
If this is sufficient, is there any test or example on units IO? I have no experience with the library, so it'll be time consuming for me to learn it to write this one test.

---

## Comment 5

> Username: jhunold  
> Created_at: 2014-09-16 09:20:53 UTC  
> Url: https://github.com/boostorg/units/pull/2#issuecomment-55717035  

Yes, there is "test/test_output.cpp" which tests at least "raw_fmt" and "symbol_fmt". Just add your dummy test code there. If you run into problems, you also might point me to the Boost.Core tests and I'll code something next weekend.

---

## Comment 6

> Username: mkurdej  
> Created_at: 2014-09-16 09:59:10 UTC  
> Updated_at: 2014-09-16 11:22:07 UTC  
> Url: https://github.com/boostorg/units/pull/2#discussion_r17592424  

This line produces a warning (turned into an error on MSVC because of <warnings-as-errors>on option) in Jamfile:  
  
```  
.../boost/units/detail/utility.hpp(32): error C2220: warning treated as error - no 'object' file generated  
.../boost/units/detail/utility.hpp(32): warning C4127: conditional expression is constant  
```  
  
Considering changing it into  
  
```  
for (;;)  
```

---

## Comment 7

> Username: mkurdej  
> Created_at: 2014-09-16 10:06:14 UTC  
> Url: https://github.com/boostorg/units/pull/2#issuecomment-55721891  

A code testing for "typename_format" can be like this, see below (passing on MSVC {11.0,12.0,14.CTP3}.  
  
If you want it to be just a dummy test, define a macro similar to `BOOST_UNITS_TEST_OUTPUT` or write simply `std::cout FORMATTERS << meter_base_unit::unit_type()` and so on.  
  
Put the following into `units/test/test_output.cpp`:  
  
```  
BOOST_AUTO_TEST_CASE(test_output_unit_typename)  
{  // name format specified.  
#define FORMATTERS << boost::units::typename_format  
    BOOST_UNITS_TEST_OUTPUT(meter_base_unit::unit_type(), "class unit<struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct dimensionless_type>,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct meter_base_unit,class static_rational<1,1> >,struct dimensionless_type>,struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct dimensionless_type>,struct dimensionless_type> >,void>");  
    BOOST_UNITS_TEST_OUTPUT(velocity(), "class unit<struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct list<struct dim<struct time_base_dimension,class static_rational<-1,1> >,struct dimensionless_type> >,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct meter_base_unit,class static_rational<1,1> >,struct list<struct heterogeneous_system_dim<struct second_base_unit,class static_rational<-1,1> >,struct dimensionless_type> >,struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct list<struct dim<struct time_base_dimension,class static_rational<-1,1> >,struct dimensionless_type> >,struct dimensionless_type> >,void>");  
    BOOST_UNITS_TEST_OUTPUT(scaled_length(), "class unit<struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct dimensionless_type>,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct meter_base_unit,class static_rational<1,1> >,struct dimensionless_type>,struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct dimensionless_type>,struct list<struct scale_list_dim<struct scale<10,class static_rational<3,1> > >,struct dimensionless_type> > >,void>");  
    BOOST_UNITS_TEST_OUTPUT(scaled_velocity1(), "class unit<struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct list<struct dim<struct time_base_dimension,class static_rational<-1,1> >,struct dimensionless_type> >,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct meter_base_unit,class static_rational<1,1> >,struct list<struct heterogeneous_system_dim<struct second_base_unit,class static_rational<-1,1> >,struct dimensionless_type> >,struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct list<struct dim<struct time_base_dimension,class static_rational<-1,1> >,struct dimensionless_type> >,struct list<struct scale_list_dim<struct scale<10,class static_rational<3,1> > >,struct dimensionless_type> > >,void>");  
    BOOST_UNITS_TEST_OUTPUT(millisecond_base_unit::unit_type(), "class unit<struct list<struct dim<struct time_base_dimension,class static_rational<1,1> >,struct dimensionless_type>,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct scaled_base_unit<struct second_base_unit,struct scale<10,class static_rational<-3,1> > >,class static_rational<1,1> >,struct dimensionless_type>,struct list<struct dim<struct time_base_dimension,class static_rational<1,1> >,struct dimensionless_type>,struct dimensionless_type> >,void>");  
    BOOST_UNITS_TEST_OUTPUT(scaled_time(), "class unit<struct list<struct dim<struct time_base_dimension,class static_rational<1,1> >,struct dimensionless_type>,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct scaled_base_unit<struct second_base_unit,struct scale<10,class static_rational<-3,1> > >,class static_rational<1,1> >,struct dimensionless_type>,struct list<struct dim<struct time_base_dimension,class static_rational<1,1> >,struct dimensionless_type>,struct dimensionless_type> >,void>");  
    BOOST_UNITS_TEST_OUTPUT(scaled_velocity2(), "class unit<struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct list<struct dim<struct time_base_dimension,class static_rational<-1,1> >,struct dimensionless_type> >,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct meter_base_unit,class static_rational<1,1> >,struct list<struct heterogeneous_system_dim<struct scaled_base_unit<struct second_base_unit,struct scale<10,class static_rational<-3,1> > >,class static_rational<-1,1> >,struct dimensionless_type> >,struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct list<struct dim<struct time_base_dimension,class static_rational<-1,1> >,struct dimensionless_type> >,struct dimensionless_type> >,void>");  
    BOOST_UNITS_TEST_OUTPUT(area(), "class unit<struct list<struct dim<struct length_base_dimension,class static_rational<2,1> >,struct dimensionless_type>,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct meter_base_unit,class static_rational<2,1> >,struct dimensionless_type>,struct list<struct dim<struct length_base_dimension,class static_rational<2,1> >,struct dimensionless_type>,struct dimensionless_type> >,void>");  
    BOOST_UNITS_TEST_OUTPUT(scaled_area(), "class unit<struct list<struct dim<struct length_base_dimension,class static_rational<2,1> >,struct dimensionless_type>,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct meter_base_unit,class static_rational<2,1> >,struct dimensionless_type>,struct list<struct dim<struct length_base_dimension,class static_rational<2,1> >,struct dimensionless_type>,struct list<struct scale_list_dim<struct scale<10,class static_rational<3,1> > >,struct dimensionless_type> > >,void>");  
    BOOST_UNITS_TEST_OUTPUT(double_scaled_length(), "class unit<struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct dimensionless_type>,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct meter_base_unit,class static_rational<1,1> >,struct dimensionless_type>,struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct dimensionless_type>,struct list<struct scale_list_dim<struct scale<2,class static_rational<10,1> > >,struct list<struct scale_list_dim<struct scale<10,class static_rational<3,1> > >,struct dimensionless_type> > > >,void>");  
    BOOST_UNITS_TEST_OUTPUT(double_scaled_length2(), "class unit<struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct dimensionless_type>,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct scaled_base_unit<struct scaled_base_unit<struct meter_base_unit,struct scale<100,class static_rational<1,1> > >,struct scale<10,class static_rational<3,1> > >,class static_rational<1,1> >,struct dimensionless_type>,struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct dimensionless_type>,struct dimensionless_type> >,void>");  
    BOOST_UNITS_TEST_OUTPUT(custom1(), "class unit<struct list<struct dim<struct length_base_dimension,class static_rational<3,1> >,struct dimensionless_type>,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct meter_base_unit,class static_rational<3,1> >,struct dimensionless_type>,struct list<struct dim<struct length_base_dimension,class static_rational<3,1> >,struct dimensionless_type>,struct dimensionless_type> >,void>");  
    BOOST_UNITS_TEST_OUTPUT(custom2(), "class unit<struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct list<struct dim<struct time_base_dimension,class static_rational<-2,1> >,struct dimensionless_type> >,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct meter_base_unit,class static_rational<1,1> >,struct list<struct heterogeneous_system_dim<struct second_base_unit,class static_rational<-2,1> >,struct dimensionless_type> >,struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct list<struct dim<struct time_base_dimension,class static_rational<-2,1> >,struct dimensionless_type> >,struct dimensionless_type> >,void>");  
    BOOST_UNITS_TEST_OUTPUT(scaled_custom1(), "class unit<struct list<struct dim<struct length_base_dimension,class static_rational<3,1> >,struct dimensionless_type>,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct meter_base_unit,class static_rational<3,1> >,struct dimensionless_type>,struct list<struct dim<struct length_base_dimension,class static_rational<3,1> >,struct dimensionless_type>,struct list<struct scale_list_dim<struct scale<10,class static_rational<3,1> > >,struct dimensionless_type> > >,void>");  
    BOOST_UNITS_TEST_OUTPUT(scaled_custom2(), "class unit<struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct list<struct dim<struct time_base_dimension,class static_rational<-2,1> >,struct dimensionless_type> >,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct meter_base_unit,class static_rational<1,1> >,struct list<struct heterogeneous_system_dim<struct second_base_unit,class static_rational<-2,1> >,struct dimensionless_type> >,struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct list<struct dim<struct time_base_dimension,class static_rational<-2,1> >,struct dimensionless_type> >,struct list<struct scale_list_dim<struct scale<10,class static_rational<3,1> > >,struct dimensionless_type> > >,void>");  
    BOOST_UNITS_TEST_OUTPUT(boost::units::absolute<meter_base_unit::unit_type>(), "absolute class unit<struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct dimensionless_type>,struct heterogeneous_system<struct heterogeneous_system_impl<struct list<struct heterogeneous_system_dim<struct meter_base_unit,class static_rational<1,1> >,struct dimensionless_type>,struct list<struct dim<struct length_base_dimension,class static_rational<1,1> >,struct dimensionless_type>,struct dimensionless_type> >,void>");  
#undef FORMATTERS  
}  
```

---

## Comment 8

> Username: Lastique  
> Created_at: 2014-09-16 11:23:11 UTC  
> Url: https://github.com/boostorg/units/pull/2#issuecomment-55728567  

Thanks for all your comments. Done.

---
