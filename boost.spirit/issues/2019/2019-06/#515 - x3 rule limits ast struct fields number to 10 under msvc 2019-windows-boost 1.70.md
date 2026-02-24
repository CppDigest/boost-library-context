# #515 - x3 rule limits ast struct fields number to 10 under msvc 2019/windows/boost 1.70 [Closed]

> Username: rockonedege  
> Created at: 2019-06-17 11:32:48 UTC  
> Updated at: 2020-06-07 11:29:37 UTC  
> Closed at: 2020-06-07 11:29:37 UTC  
> Url: https://github.com/boostorg/spirit/issues/515  

Here  a rule that complies OK with clang under Linux, but fails with pages of error with MSVC 2019/Windows/Boost 1.70.  
  
- ast:  
```cpp  
struct mid0061_data_rev1 : mid_base<61, mid0061::revision::_1>  
{  
  std::uint32_t cell_id;  
  std::uint8_t channel_id;  
  std::string torque_controller_name; // 25 bytes  
  std::string vin_id;                 // 25 bytes  
  std::uint8_t job_id;  
    
  std::uint16_t parameter_set_id;  
  std::uint32_t batch_size;  
  std::uint32_t batch_counter;  
  std::uint8_t tightening_status;  
  std::uint8_t torque_status;  
  std::uint8_t angle_status;  
  
  // 6 bytes each  
  std::uint32_t torque_min_limit;  
  std::uint32_t torque_max_limit;  
  std::uint32_t torque_final_target;  
  std::uint32_t torque;  
  // 5 bytes each  
  std::uint32_t angle_min_limit;  
  std::uint32_t angle_max_limit;  
  std::uint32_t angle_final_target;  
  std::uint32_t angle;  
  
  std::string timestamp_tightening;                    // 19 byte  
  std::string timestamp_last_parameter_setting_change; // 19 byte  
  
  std::uint8_t batch_status;  
  std::uint32_t tightening_id; // 10 byte  
};  
```  
  
- rule:  
```cpp  
namespace x3 = boost::spirit::x3;  
  
auto const uint_1_digit = x3::uint_parser<std::uint8_t, 10, 1, 1>{};  
auto const uint_2_digits = x3::uint_parser<std::uint8_t, 10, 2, 2>{};  
auto const uint_3_digits = x3::uint_parser<std::uint32_t, 10, 3, 3>{};  
auto const uint_4_digits = x3::uint_parser<std::uint32_t, 10, 4, 4>{};  
auto const uint_5_digits = x3::uint_parser<std::uint32_t, 10, 5, 5>{};  
auto const uint_6_digits = x3::uint_parser<std::uint32_t, 10, 6, 6>{};  
auto const uint_10_digits = x3::uint_parser<std::uint32_t, 10, 10, 10>{};  
  
auto const r_def = lit("01") >> uint_4_digits            // cell_id;  
                  //  >> lit("02") >> uint_2_digits         // channel_id;  
                  //  >> lit("03") >> repeat(25)[x3::char_] // torque_controller_name;  
                  //  >> lit("04") >> repeat(25)[x3::char_] // vin_id;  
                  //  >> lit("05") >> uint_2_digits         // job_id;  
                  //  >> lit("06") >> uint_3_digits         // parameter_set_id;  
  
                  //  >> lit("07") >> uint_4_digits // batch_size;  
                  //  >> lit("08") >> uint_4_digits // batch_counter;  
                  //  >> lit("09") >> uint_1_digit  // tightening_status;  
                  //  >> lit("10") >> uint_1_digit  // torque_status;  
                  //  >> lit("11") >> uint_1_digit  // angle_status;  
  
                  //  >> lit("12") >> uint_6_digits // torque_min_limit;  
                  //  >> lit("13") >> uint_6_digits // torque_max_limit;  
                  //  >> lit("14") >> uint_6_digits // torque_finalarget;  
                   >> lit("15") >> uint_6_digits // torque;  
  
                   >> lit("16") >> uint_5_digits // angle_min_limit;  
                   >> lit("17") >> uint_5_digits // angle_max_limit;  
                   >> lit("18") >> uint_5_digits // angle_finalarget;  
                   >> lit("19") >> uint_5_digits // angle;  
  
                   >> lit("20") >> repeat(19)[x3::char_] //  timestamp; // 19 byte  
                   >> lit("21") >> repeat(19)[x3::char_] //  timestamp; // 19 byte  
  
                   >> lit("22") >> uint_1_digit   //  batch_status;  
                   >> lit("23") >> uint_10_digits //  tightening_id; // 10 byte  
    ;  
```  
- adaptation  
  
```cpp  
  
BOOST_FUSION_ADAPT_STRUCT(open_protocol::ast::tightening::mid0061_data_rev1,  
                          cell_id,  
                          // channel_id,  
                          // torque_controller_name,  
                          // vin_id,  
                          // job_id,  
                          // parameter_set_id,  
  
                          // batch_size,  
                          // batch_counter,  
                          // tightening_status,  
                          // torque_status,  
                          // angle_status,  
  
                          // torque_min_limit,  
                          // torque_max_limit,  
                          // torque_final_target,  
                          torque,  
  
                          angle_min_limit,  
                          angle_max_limit,  
                          angle_final_target,  
                          angle,  
  
                          timestamp_tightening,  
                          timestamp_last_parameter_setting_change,  
  
                          batch_status,  
                          tightening_id  
                          )  
  
```  
the error says it has something to do with the rule def.  
The error is too long to past here, which I could do if requested.  
  
While debugging it starting with one field and adding another. I found that it not the parsers but the total number of parsers that fails the compilation.  
  
selecting **any combination of 10 or under** will compile just fine. One more will fail it.  
  
I suspect it has something to do with BOOST_FUSION_ADAPT_STRUCT with MSVC, a default limit is set somewhere.  
  
Is this by design or a bug in x3 or MSVC? How can I workaround it?

---

## Comment 1

> Username: djowel  
> Created at: 2019-06-17 22:26:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/515#issuecomment-502873876  

I suspect this falls more under Fusion, rather than Spirit. Please provide a minimum compilable self contained test code, plus the errors, so we can determine where this issue falls under.

---

## Comment 2

> Username: rockonedege  
> Created at: 2019-06-18 05:37:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/515#issuecomment-502954676  

Thanks for investigating.   
  
Below is the minimum  example code that fails to compile. It compiles ok if keep the struct member count under 10.  
  
``` cpp  
#include <boost/config/warning_disable.hpp>  
#include <boost/spirit/home/x3.hpp>  
  
#include <boost/fusion/include/adapt_struct.hpp>  
  
  
namespace open_protocol::ast::tightening  
{  
  
struct mid0061_data_rev1  
{  
  std::uint32_t cell_id;  
  std::uint8_t channel_id;  
  std::string torque_controller_name; // 25 bytes  
  std::string vin_id;                 // 25 bytes  
  std::uint8_t job_id;  
  std::uint16_t parameter_set_id;  
  
  std::uint32_t batch_size;  
  std::uint32_t batch_counter;  
  std::uint8_t tightening_status;  
  std::uint8_t torque_status;  
  std::uint8_t angle_status;  
  
  // 6 bytes each  
  std::uint32_t torque_min_limit;  
  std::uint32_t torque_max_limit;  
  std::uint32_t torque_final_target;  
  std::uint32_t torque;  
  
  // 5 bytes each  
  std::uint32_t angle_min_limit;  
  std::uint32_t angle_max_limit;  
  std::uint32_t angle_final_target;  
  std::uint32_t angle;  
  
  std::string timestamp_tightening;                    // 19 bytes  
  std::string timestamp_last_parameter_setting_change; // 19 bytes  
  
  std::uint8_t batch_status;  
  std::uint32_t tightening_id; // 10 bytes  
};  
} // namespace open_protocol::ast::tightening  
  
  
BOOST_FUSION_ADAPT_STRUCT(open_protocol::ast::tightening::mid0061_data_rev1,  
                          cell_id,  
                          channel_id,  
                          torque_controller_name,  
                          vin_id,  
                          job_id,  
                          parameter_set_id,  
  
                          batch_size,  
                          batch_counter,  
                          tightening_status,  
                          torque_status,  
                          angle_status,  
  
                          torque_min_limit,  
                          torque_max_limit,  
                          torque_final_target,  
                          torque,  
  
                          angle_min_limit,  
                          angle_max_limit,  
                          angle_final_target,  
                          angle,  
  
                          timestamp_tightening,  
                          timestamp_last_parameter_setting_change,  
  
                          batch_status,  
                          tightening_id  
                          )  
  
namespace open_protocol::parser::tightening::mid0061_data_rev1  
{  
namespace x3 = boost::spirit::x3;  
namespace ascii = x3::ascii;  
  
using x3::lit;  
using x3::repeat;  
  
auto const uint_1_digit = x3::uint_parser<std::uint8_t, 10, 1, 1>{};  
auto const uint_2_digits = x3::uint_parser<std::uint8_t, 10, 2, 2>{};  
auto const uint_3_digits = x3::uint_parser<std::uint32_t, 10, 3, 3>{};  
auto const uint_4_digits = x3::uint_parser<std::uint32_t, 10, 4, 4>{};  
auto const uint_5_digits = x3::uint_parser<std::uint32_t, 10, 5, 5>{};  
auto const uint_6_digits = x3::uint_parser<std::uint32_t, 10, 6, 6>{};  
auto const uint_10_digits = x3::uint_parser<std::uint32_t, 10, 10, 10>{};  
  
using rule_type = x3::rule<class mid0061_data_rev1_class, open_protocol::ast::tightening::mid0061_data_rev1>;  
  
rule_type const r = "mid0061_data_rev1";  
  
auto const r_def = lit("01") >> uint_4_digits               // cell_id;  
                   >> lit("02") >> uint_2_digits            // channel_id;  
                   >> lit("03") >> repeat(25)[ascii::char_] // torque_controller_name;  
                   >> lit("04") >> repeat(25)[ascii::char_] // vin_id;  
                   >> lit("05") >> uint_2_digits            // job_id;  
                   >> lit("06") >> uint_3_digits            // parameter_set_id;  
  
                   >> lit("07") >> uint_4_digits // batch_size;  
                   >> lit("08") >> uint_4_digits // batch_counter;  
                   >> lit("09") >> uint_1_digit  // tightening_status;  
                   >> lit("10") >> uint_1_digit  // torque_status;  
                   >> lit("11") >> uint_1_digit  // angle_status;  
  
                   >> lit("12") >> uint_6_digits // torque_min_limit;  
                   >> lit("13") >> uint_6_digits // torque_max_limit;  
                   >> lit("14") >> uint_6_digits // torque_finalarget;  
                   >> lit("15") >> uint_6_digits // torque;  
  
                   >> lit("16") >> uint_5_digits            // angle_min_limit;  
                   >> lit("17") >> uint_5_digits            // angle_max_limit;  
                   >> lit("18") >> uint_5_digits            // angle_finalarget;  
                   >> lit("19") >> uint_5_digits            // angle;  
  
  
                   >> lit("20") >> repeat(19)[ascii::char_] //  timestamp; // 19 bytes  
                   >> lit("21") >> repeat(19)[ascii::char_] //  timestamp; // 19 bytes  
                   >> lit("22") >> uint_1_digit             //  batch_status;  
                   >> lit("23") >> uint_10_digits           //  tightening_id; // 10 bytes  
    ;  
  
} // namespace open_protocol::parser::tightening::mid0061_data_rev1  
  
namespace open_protocol::parser::tightening::mid0061_data_rev1  
{  
BOOST_SPIRIT_DEFINE(r);  
  
rule_type rule()  
{  
  return r;  
}  
  
using iterator_type = std::string::const_iterator;  
BOOST_SPIRIT_INSTANTIATE(rule_type, iterator_type, x3::unused_type);  
} // namespace open_protocol::parser::tightening::mid0061_data_rev1  
  
int main()  
{  
}  
```

---

## Comment 3

> Username: djowel  
> Created at: 2019-06-19 02:56:41 UTC  
> Url: https://github.com/boostorg/spirit/issues/515#issuecomment-503386146  

Thanks. I'll have a look as soon as I can. I do not have MSVC 2019 at the moment.

---

## Comment 4

> Username: rockonedege  
> Created at: 2019-06-20 14:24:49 UTC  
> Updated at: 2019-06-20 14:59:25 UTC  
> Url: https://github.com/boostorg/spirit/issues/515#issuecomment-504046402  

Yesterday, I updated vs2017 to the latest patch and found the error threshold to be 20, which happens to be the default [mpl::vector size](https://www.boost.org/doc/libs/1_70_0/libs/mpl/doc/refmanual/limit-vector-size.html), as I noticed `vector20` from the error message.  
  
 Trying to prepend the following lines at the very top does make it to `vector30` , the compilation still failed.  
```cpp  
#define BOOST_MPL_CFG_NO_PREPROCESSED_HEADERS  
#define BOOST_MPL_LIMIT_VECTOR_SIZE 30  
```  
it looks to have something to do with `mpl`,  but I am not sure where the problem is.

---

## Comment 5

> Username: rockonedege  
> Created at: 2019-06-20 14:44:08 UTC  
> Updated at: 2019-06-20 14:59:39 UTC  
> Url: https://github.com/boostorg/spirit/issues/515#issuecomment-504054098  

my bad, setting `#define BOOST_MPL_LIMIT_VECTOR_SIZE 50` fixed it. No I wonder why it's not a problem with Clang/Linux.

---

## Comment 6

> Username: djowel  
> Created at: 2019-06-20 23:47:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/515#issuecomment-504237963  

> my bad, setting `#define BOOST_MPL_LIMIT_VECTOR_SIZE 50` fixed it. No I wonder why it's not a problem with Clang/Linux.  
  
I suspect it's because some code are on different #ifdef code paths, because of late c++ conformance with MSVC compilers. I'd still want to investigate, so let's keep this open.
