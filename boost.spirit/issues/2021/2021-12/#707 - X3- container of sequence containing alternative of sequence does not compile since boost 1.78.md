# #707 - X3: container of sequence containing alternative of sequence does not compile since boost 1.78 [Open]

> Username: intractabilis  
> Created at: 2021-12-27 08:00:38 UTC  
> Updated at: 2025-05-09 23:44:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/707  

After upgrade to boost 1.78, my grammar doesn't compile. Consider  
```c++  
#include <cstdio>  
#include <string>  
#include <vector>  
  
#include <boost/fusion/adapted/struct/adapt_struct.hpp>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/variant.hpp>  
  
namespace x3 = boost::spirit::x3;  
  
struct S1 {  
    double           bStar;  
    uint64_t         f1;  
    uint64_t         f2;  
    std::vector<int> f3;  
};  
BOOST_FUSION_ADAPT_STRUCT(S1, bStar, f1, f2, f3);  
  
struct S2 {  
    uint64_t         f1;  
    uint64_t         f2;  
    std::vector<int> f3;  
};  
BOOST_FUSION_ADAPT_STRUCT(S2, f1, f2, f3);  
  
struct LogData {  
    int      f3;  
    int      f4;  
    uint64_t f5;  
    boost::variant<S1, S2> f6;  
};  
BOOST_FUSION_ADAPT_STRUCT(LogData, f3, f4, f5, f6);  
  
int main(int argc, char *argv[]) {  
    auto s = std::string{  
R"(  39 45 13833 1.186186 2796264 221200 {13,  9,  4, 10,  2,  3}  
     34 44 45264 1.227986 2796264 884752 {14,  4,  0,  9, 11,  2})"};  
    auto dataSet = std::vector<LogData>{};  
    if (x3::phrase_parse(  
        s.begin(), s.end(),  
        (  
            x3::int_ >> x3::int_ >> x3::uint64  
         >> (  
                (  
                    x3::double_ >> x3::uint64 >> x3::uint64  
                 >> '{' >> (x3::int_ % ',') >> '}'  
                )  
              | (  
                    x3::uint64 >> x3::uint64  
                 >> '{' >> (x3::int_ % ',') >> '}'  
                )  
            )  
        ) % x3::eol,  
        x3::blank,  
        dataSet  
    ))  
    {  
        printf("Parsing success\n");  
    }  
    else  
    {  
        printf("Parsing failed\n");  
    }  
}  
```  
It worked with boost 1.77. With 1.78 the compiler outputs almost 500 lines of incomprehensible messages. The gist of it is, `boost::spirit::x3::detail::parse_into_container_impl` fails. It compiles with the following changes:  
```c++  
    auto dataSet = LogData{};  
    if (x3::phrase_parse(  
        s.begin(), s.end(),  
        (  
            x3::int_ >> x3::int_ >> x3::uint64  
         >> (  
                (  
                    x3::double_ >> x3::uint64 >> x3::uint64  
                 >> '{' >> (x3::int_ % ',') >> '}'  
                )  
              | (  
                    x3::uint64 >> x3::uint64  
                 >> '{' >> (x3::int_ % ',') >> '}'  
                )  
            )  
        )  
        x3::blank,  
        dataSet  
    ))  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2021-12-27 20:55:42 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1001748725  

Thanks for the short reproducer. Though the example indeed regressed with https://github.com/boostorg/spirit/pull/702 it just exposed a bug in `is_substitute`, which incorrectly handles a case when both parameters are variants https://github.com/boostorg/spirit/blob/abd946b00d8726f1dd586860b2f191b202ff105a/include/boost/spirit/home/x3/support/traits/is_substitute.hpp#L73-L79  
fixing that triggers a bug in `attr` parser/`move_to` because there seems to be a long standing issue related to `is_substitute` actually being used to also find weak substitutes despite of being documented as finding a strong substitute. The sequence parser tuple/sequence mode deduce logic should be been tripping on that regularly,,,,  
For now I don't see a clean way to resolve this, I also had a huge desire to get rid of `is_substitute` because I predicted issues in it.

---

## Comment 2

> Username: Kojoley  
> Created at: 2021-12-28 00:07:58 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1001805868  

Reverting will break code too, and I don't think it is a good idea to cascade revert already released changes which itself doesn't contain bugs.

---

## Comment 3

> Username: Kojoley  
> Created at: 2022-01-04 14:57:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1004880586  

> I don't understand. You said there is a bug in the current code. Now there is no bug. Can you explain?  
  
It's fine that you don't understand and I can explain clearer if you ask nicely, but please don't put words I in my mouth that I never said.  
  
> From my perspective, if it breaks legitimate old code, it's a bug. I hope you understand the maintenance nightmare you are forcing my team into: you can't suggest how to fix the code that worked before, and you refuse to roll back the changes.  
  
I would strongly consider a revert if you have reported the issue during the beta which is meant exactly for that, but you did it after the release. Any decision I made now will upset somebody. If the issue is critically important to you - you can invest your resources and help fixing it. I could've shown you how to change your code to workaround the issue if you have had asked me.  
  
> There must be at least a patch on the release notes page in the Known Issues section. There are already two patches there: [boost.org/patches/1_78_0/0001-b2-fix-install.patch](https://www.boost.org/patches/1_78_0/0001-b2-fix-install.patch), [boost.org/patches/1_78_0/0002-atomic-fix-uwp-build.patch](https://www.boost.org/patches/1_78_0/0002-atomic-fix-uwp-build.patch).  
  
There is no patch at the moment I could put there.

---

## Comment 4

> Username: Kojoley  
> Created at: 2022-01-04 16:09:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1004940026  

Again, reverting the commit is not a fix. The patches on the page are for fixes which different package managers do incorporate in their releases of Boost.

---

## Comment 5

> Username: Kojoley  
> Created at: 2022-01-04 17:48:11 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1005038127  

I suggest to stop nagging and guilt-tripping. I strongly encourage to read [the license](https://www.boost.org/LICENSE_1_0.txt).  
  
You have three options:  
1. Workaround the issue in your code.  
2. Help fixing the bug.  
3. Patiently wait for a fix by someone else.

---

## Comment 6

> Username: Kojoley  
> Created at: 2022-01-04 19:01:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1005089206  

Closing the ticket helps no one. Please refrain from using manipulation tactics.

---

## Comment 7

> Username: cppljevans  
> Created at: 2022-01-04 21:08:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1005171974  

Nikita, intractabilis:  
  
I have investigated this and found a solution, although one that's   
probably not very palatable.  
  
Here's the code:  
  
//OriginalSource:  
//  copy&pasted on 2021-12-29.0553CST from 1st code listing here:  
/*  
https://github.com/boostorg/spirit/issues/707  
  */  
//===================  
#include <iostream>  
#include <string>  
#include <vector>  
  
#include <boost/fusion/adapted/struct/adapt_struct.hpp>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/traits/print_attribute.hpp>  
#ifdef USE_UNFOLD_LEFT  
#include <boost/spirit/home/x3/core/make_transform_parser.hpp>  
#define USE_TUPLE_TIE  
#ifdef USE_TUPLE_TIE  
#include <boost/fusion/tuple/tuple_tie.hpp>  
#endif//USE_TUPLE_TIE  
#endif//USE_UNFOLD_LEFT  
  
namespace x3 = boost::spirit::x3;  
  
using f3_ast = std::vector<int>;  
struct S1 {  
     double   bStar;  
     uint64_t f1;  
     uint64_t f2;  
     f3_ast   f3;  
};  
BOOST_FUSION_ADAPT_STRUCT(S1, bStar, f1, f2, f3);  
  
struct S2 {  
     uint64_t f1;  
     uint64_t f2;  
     f3_ast   f3;  
};  
BOOST_FUSION_ADAPT_STRUCT(S2, f1, f2, f3);  
  
#ifdef USE_UNFOLD_LEFT  
using f6_ast=x3::std_variant<S1, S2>;  
#else  
using f6_ast=boost::variant<S1, S2>;  
#endif  
  
struct LogData {  
     int      f3;  
     int      f4;  
     uint64_t f5;  
     f6_ast   f6;  
};  
BOOST_FUSION_ADAPT_STRUCT(LogData, f3, f4, f5, f6);  
  
auto f3_parser  
     = '{'  
     >> (x3::int_ % ',')  
     >> '}'  
     ;  
#ifdef USE_UNFOLD_LEFT  
namespace boost { namespace spirit { namespace x3 { namespace traits  
{  
   using f3_attr=x3::attribute_of_default<decltype(f3_parser)>;  
  
   template  
   <  
   >  
   struct  
transform_attribute  
   < f3_ast  
   , f3_attr  
   , transform_parser_id  
   >  
   {  
       using to_attribute_type=f3_ast;  
     #ifdef USE_TUPLE_TIE  
       static auto pre(to_attribute_type& to_attr)  
       { return boost::fusion::tie(unused,to_attr,unused);  
       }  
  
       template<typename From, typename To>  
       static void post(From&, To&)  
       {  //do nothing since the pre function copied this to_attr ref  
          //to the value passed to the   
transform_parser_attribute::from_parser.  
       }  
     #else  
       using from_attribute_type=f3_attr;  
       static auto pre(to_attribute_type& to_attr)  
       { return from_attribute_type();  
       }  
  
       static void post(to_attribute_type& to_attr, from_attribute_type&   
from_attr)  
       {  
           //traits::move_to(from_attr, to_attr);  
           to_attr=at_c<1>(from_attr);  
       }  
     #endif//USE_TYPLE_TIE  
   };  
}}}}  
auto f3_xform  
     = x3::make_transform_parser<f3_ast>(f3_parser)  
     ;  
#else  
auto f3_xform  
     = f3_parser  
     ;  
#endif//USE_UNFOLD_LEFT  
int main(int argc, char *argv[]) {  
     auto s = std::string  
     {  
R"(  39 45 13833 1.186186 2796264 221200 {13,  9,  4, 10,  2,  3}  
      34 44 45264 1.227986 2796264 884752 {14,  4,  0,  9, 11,  2}  
      44 55 66666          7777777 888888 {10, 20,  0, 30, 40, 50}  
   )"  
      };  
     auto dataSet = std::vector<LogData>{};  
     if (x3::phrase_parse(  
         s.begin(), s.end(),  
         (  
             x3::int_ >> x3::int_ >> x3::uint64  
          >> (  
                 (  
                     x3::double_ >> x3::uint64 >> x3::uint64  
                  >> f3_xform  
                 )  
               | (  
                     x3::uint64 >> x3::uint64  
                  >> f3_xform  
                 )  
             )  
         ) % x3::eol,  
         x3::blank,  
         dataSet  
     ))  
     {  
         std::cout<<"Parsing success\n";  
         for(unsigned i=0; i<dataSet.size(); ++i)  
         {  
           std::cout<<"dataSet["<<i<<"]=\n";  
           x3::traits::print_attribute(std::cout,dataSet[i]);  
           std::cout<<";\n";  
         }  
     }  
     else  
     {  
         std::cout<<"Parsing failed\n";  
     }  
}  
  
and here's the run output:  
  
c:/msys64/tmp/build/clangxx11_0_0/boost.org/boost.replacements/examples/realworld/issue707/1.78breakage.exe   
  
Parsing success  
dataSet[0]=  
tuple{39, 45, 13833, variant( 0, tuple{1.18619, 2796264, 221200,   
container[13, 9, 4, 10, 2, 3]})};  
dataSet[1]=  
tuple{34, 44, 45264, variant( 0, tuple{1.22799, 2796264, 884752,   
container[14, 4, 0, 9, 11, 2]})};  
dataSet[2]=  
tuple{44, 55, 66666, variant( 1, tuple{7777777, 888888, container[10,   
20, 0, 30, 40, 50]})};  
  
Compilation finished at Tue Jan  4 14:58:00  
  
which clearly shows there **IS** a way to fix the problem, and, although   
the changes the spirit are not shown,  
  
it does show there **IS** a workaround, although not an ideal one(due to   
the USE_TUPLE_TIE post's need for fusion::tie).  
  
On 1/4/2022 1:03 PM, intractabilis wrote:  
  
> Gosh... I wash my hands.  
>  
> —  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/spirit/issues/707#issuecomment-1005090962>,   
> or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/AAEFBAZZ6P742UXDJ75WFLDUUNAAJANCNFSM5KZ5JMTQ>.  
> Triage notifications on the go with GitHub Mobile for iOS   
> <https://apps.apple.com/app/apple-store/id1477376905?ct=notification-email&mt=8&pt=524675>   
> or Android   
> <https://play.google.com/store/apps/details?id=com.github.android&referrer=utm_campaign%3Dnotification-email%26utm_medium%3Demail%26utm_source%3Dgithub>.   
>  
> You are receiving this because you are subscribed to this   
> thread.Message ID: ***@***.***>  
>

---

## Comment 8

> Username: cppljevans  
> Created at: 2022-01-05 17:24:10 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1005924101  

In addition, redefining the move_to<Source,LogData> to just print the values shows that the parser parses  
all the values but attempts to move them all to a LogData instead of the fields of LogData.  
Here's the current code:  
```c++  
//OriginalSource:  
//  copy&pasted on 2021-12-29.0553CST from 1st code listing here:  
/*  
https://github.com/boostorg/spirit/issues/707  
 */  
//===================  
#include <iostream>  
#include <string>  
#include <vector>  
  
using f3_ast = std::vector<int>;  
struct S1 {  
    double   bStar;  
    uint64_t f1;  
    uint64_t f2;  
    f3_ast   f3;  
};  
  
struct S2 {  
    uint64_t f1;  
    uint64_t f2;  
    f3_ast   f3;  
};  
  
#ifdef USE_UNFOLD_LEFT  
  #include <boost/spirit/home/x3/support/ast/std_variant.hpp>  
  using f6_ast=boost::spirit::x3::std_variant<S1, S2>;  
#else  
  #include <boost/variant.hpp>  
  using f6_ast=boost::variant<S1, S2>;  
#endif//USE_UNFOLD_LEFT  
  
struct LogData {  
    int      f3;  
    int      f4;  
    uint64_t f5;  
    f6_ast   f6;  
};  
  
auto dataSet = std::vector<LogData>{};  
  
#define MOVE_TO_INT_LOGDATA  
#ifdef MOVE_TO_INT_LOGDATA  
namespace boost{namespace spirit{namespace x3{namespace traits{  
    template<typename Source>  
    inline void move_to(Source& src, LogData& dest)  
    {  
        unsigned i=dataSet.size();  
        dest.f3=i;  
        std::cout<<__LINE__<<':'<<__func__<<":size="<<i<<":src="<<src<<";\n";  
    }  
}}}}  
#endif//MOVE_TO_INT_LOGDATA  
#include <boost/fusion/adapted/struct/adapt_struct.hpp>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/traits/print_attribute.hpp>  
  
BOOST_FUSION_ADAPT_STRUCT(S1, bStar, f1, f2, f3);  
BOOST_FUSION_ADAPT_STRUCT(S2, f1, f2, f3);  
BOOST_FUSION_ADAPT_STRUCT(LogData, f3, f4, f5, f6);  
  
namespace x3 = boost::spirit::x3;  
auto f3_content  
  = x3::int_ % ','  
  ;  
auto f3_parser   
  = '{'   
  >> f3_content  
  >> '}'  
  ;  
#ifdef USE_UNFOLD_LEFT  
  #include <boost/spirit/home/x3/core/make_transform_parser.hpp>  
  #define USE_TUPLE_TIE  
  #ifdef USE_TUPLE_TIE  
    #include <boost/fusion/tuple/tuple_tie.hpp>  
  #endif//USE_TUPLE_TIE  
namespace boost{namespace spirit{namespace x3{namespace traits{  
  using f3_attr=x3::attribute_of_default<decltype(f3_parser)>;  
  
  template  
  <  
  >  
  struct  
transform_attribute  
  < f3_ast  
  , f3_attr  
  , transform_parser_id  
  >  
  {  
      using to_attribute_type=f3_ast;  
    #ifdef USE_TUPLE_TIE  
      static auto pre(to_attribute_type& to_attr)   
      { return boost::fusion::tie(unused,to_attr,unused);  
      }  
        
      template<typename From, typename To>  
      static void post(From&, To&)  
      {  //do nothing since the pre function copied this to_attr ref   
         //to the value passed to the transform_parser_attribute::from_parser.  
      }  
    #else    
      using from_attribute_type=f3_attr;  
      static auto pre(to_attribute_type& to_attr)   
      { return from_attribute_type();   
      }  
    
      static void post(to_attribute_type& to_attr, from_attribute_type& from_attr)  
      {  
          //traits::move_to(from_attr, to_attr);  
          to_attr=at_c<1>(from_attr);  
      }  
    #endif//USE_TYPLE_TIE  
  };   
}}}}  
auto f3_xform  
    = x3::make_transform_parser<f3_ast>(f3_parser)  
    ;  
#else      
auto f3_xform  
    = f3_parser  
    ;  
#endif//USE_UNFOLD_LEFT  
int main(int argc, char *argv[]) {  
    auto s = std::string  
    {  
R"(  39 45 13833 1.186186 2796264 221200 {13,  9,  4, 10,  2,  3}  
     34 44 45264 1.227986 2796264 884752 {14,  4,  0,  9, 11,  2}  
     44 55 66666          7777777 888888 {10, 20,  0, 30, 40, 50}  
  )"  
     };  
    if (x3::phrase_parse(  
        s.begin(), s.end(),  
        (    
            x3::int_ >> x3::int_ >> x3::uint64  
         >> (  
                (  
                    x3::double_ >> x3::uint64 >> x3::uint64  
                 >> f3_xform  
                )  
              | (  
                    x3::uint64 >> x3::uint64  
                 >> f3_xform  
                )  
            )  
        ) % x3::eol,  
        x3::blank,  
        dataSet  
    ))  
    {  
        std::cout<<"Parsing success\n";  
        for(unsigned i=0; i<dataSet.size(); ++i)  
        {  
          std::cout<<"dataSet["<<i<<"]=\n";  
          x3::traits::print_attribute(std::cout,dataSet[i]);  
          std::cout<<";\n";  
        }  
    }  
    else  
    {  
        std::cout<<"Parsing failed\n";  
    }  
}  
````  
and here's the output:  
````  
c:/msys64/tmp/build/clangxx11_0_0/boost.org/boost.replacements/examples/realworld/issue707/1.78breakage.exe   
51:move_to:size=0:src=39;  
51:move_to:size=0:src=45;  
51:move_to:size=0:src=13833;  
51:move_to:size=0:src=1.18619;  
51:move_to:size=0:src=2796264;  
51:move_to:size=0:src=221200;  
51:move_to:size=0:src=13;  
51:move_to:size=0:src=9;  
51:move_to:size=0:src=4;  
51:move_to:size=0:src=10;  
51:move_to:size=0:src=2;  
51:move_to:size=0:src=3;  
51:move_to:size=12:src=34;  
51:move_to:size=12:src=44;  
51:move_to:size=12:src=45264;  
51:move_to:size=12:src=1.22799;  
51:move_to:size=12:src=2796264;  
51:move_to:size=12:src=884752;  
51:move_to:size=12:src=14;  
51:move_to:size=12:src=4;  
51:move_to:size=12:src=0;  
51:move_to:size=12:src=9;  
51:move_to:size=12:src=11;  
51:move_to:size=12:src=2;  
51:move_to:size=24:src=44;  
51:move_to:size=24:src=55;  
51:move_to:size=24:src=66666;  
51:move_to:size=24:src=7.77778e+06;  
51:move_to:size=24:src=888888;  
51:move_to:size=24:src=7777777;  
51:move_to:size=24:src=888888;  
51:move_to:size=24:src=10;  
51:move_to:size=24:src=20;  
51:move_to:size=24:src=0;  
51:move_to:size=24:src=30;  
51:move_to:size=24:src=40;  
51:move_to:size=24:src=50;  
Parsing success  
dataSet[0]=  
[0, 0, 0, [0, 0, 0, []]];  
dataSet[1]=  
[0, 0, 0, [0, 0, 0, []]];  
dataSet[2]=  
[0, 0, 0, [0, 0, 0, []]];  
dataSet[3]=  
[0, 0, 0, [0, 0, 0, []]];  
dataSet[4]=  
[0, 0, 0, [0, 0, 0, []]];  
dataSet[5]=  
[0, 0, 0, [0, 0, 0, []]];  
dataSet[6]=  
[0, 0, 0, [0, 0, 0, []]];  
dataSet[7]=  
[0, 0, 0, [0, 0, 0, []]];  
dataSet[8]=  
[0, 0, 0, [0, 0, 0, []]];  
dataSet[9]=  
[0, 0, 0, [0, 0, 0, []]];  
dataSet[10]=  
[0, 0, 0, [0, 0, 0, []]];  
dataSet[11]=  
[0, 0, 0, [0, 0, 0, []]];  
dataSet[12]=  
[12, 0, 0, [0, 0, 0, []]];  
dataSet[13]=  
[12, 0, 0, [0, 0, 0, []]];  
dataSet[14]=  
[12, 0, 0, [0, 0, 0, []]];  
dataSet[15]=  
[12, 0, 0, [0, 0, 0, []]];  
dataSet[16]=  
[12, 0, 0, [0, 0, 0, []]];  
dataSet[17]=  
[12, 0, 0, [0, 0, 0, []]];  
dataSet[18]=  
[12, 0, 0, [0, 0, 0, []]];  
dataSet[19]=  
[12, 0, 0, [0, 0, 0, []]];  
dataSet[20]=  
[12, 0, 0, [0, 0, 0, []]];  
dataSet[21]=  
[12, 0, 0, [0, 0, 0, []]];  
dataSet[22]=  
[12, 0, 0, [0, 0, 0, []]];  
dataSet[23]=  
[12, 0, 0, [0, 0, 0, []]];  
dataSet[24]=  
[24, 0, 0, [0, 0, 0, []]];  
dataSet[25]=  
[24, 0, 0, [0, 0, 0, []]];  
dataSet[26]=  
[24, 0, 0, [0, 0, 0, []]];  
dataSet[27]=  
[24, 0, 0, [0, 0, 0, []]];  
dataSet[28]=  
[24, 0, 0, [0, 0, 0, []]];  
dataSet[29]=  
[24, 0, 0, [0, 0, 0, []]];  
dataSet[30]=  
[24, 0, 0, [0, 0, 0, []]];  
dataSet[31]=  
[24, 0, 0, [0, 0, 0, []]];  
dataSet[32]=  
[24, 0, 0, [0, 0, 0, []]];  
dataSet[33]=  
[24, 0, 0, [0, 0, 0, []]];  
dataSet[34]=  
[24, 0, 0, [0, 0, 0, []]];  
  
Compilation finished at Wed Jan  5 11:03:04  
````  
Hopefully the above gives further clues of where 1.78 is going wrong.

---

## Comment 9

> Username: cppljevans  
> Created at: 2022-01-25 15:55:43 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1021335174  

The problem is solved by changing is_substitute.hpp as shown below:  
````  
-*- mode: compilation; default-directory: "~/prog_dev/boost.org/boost.replacements/examples/realworld/issue707/" -*-  
Compilation started at Tue Jan 25 09:45:34  
  
make -k --always-make show-diff run  
diff /home/evansl/prog_dev/boost.org/boost.replacements/issue707/boost/spirit/home/x3/support/traits/is_substitute.hpp /home/evansl/prog_dev/boost.org/1_78_0download/boost_1_78_0/boost/spirit/home/x3/support/traits/is_substitute.hpp  
76c76  
<                 is_variant<T>  
---  
>                 is_variant<Attribute>  
78c78  
<           : variant_has_substitute<T, Attribute>  
---  
>           : variant_has_substitute<Attribute, T>  
make: *** [Makefile:72: show-diff] Error 1  
clang++ -c -O0 -gdwarf-2 -g3  -std=c++20 -ftemplate-backtrace-limit=0 -fdiagnostics-show-template-tree -fno-elide-type -fmacro-backtrace-limit=0 -fexceptions -fcxx-exceptions   -fno-diagnostics-show-option  -I/home/evansl/prog_dev/boost.org/boost.replacements/issue707 -I/home/evansl/prog_dev/boost.org/1_78_0download/boost_1_78_0     -ftemplate-depth=200  1.78breakage.cpp -MMD -o c:/msys64/tmp/build/clangxx11_0_0/boost.org/boost.replacements/examples/realworld/issue707-78/1.78breakage.o   
clang++    c:/msys64/tmp/build/clangxx11_0_0/boost.org/boost.replacements/examples/realworld/issue707-78/1.78breakage.o   -o c:/msys64/tmp/build/clangxx11_0_0/boost.org/boost.replacements/examples/realworld/issue707-78/1.78breakage.exe  
c:/msys64/tmp/build/clangxx11_0_0/boost.org/boost.replacements/examples/realworld/issue707-78/1.78breakage.exe   
Parsing success  
dataSet[0]=  
[39, 45, 13833, [1.18619, 2796264, 221200, [13, 9, 4, 10, 2, 3]]];  
dataSet[1]=  
[34, 44, 45264, [1.22799, 2796264, 884752, [14, 4, 0, 9, 11, 2]]];  
dataSet[2]=  
[44, 55, 66666, [7777777, 888888, [10, 20, 0, 30, 40, 50]]];  
  
Compilation exited abnormally with code 2 at Tue Jan 25 09:45:38  
  
````  
where the 1.78breakage.cpp file is:  
````c++  
//OriginalSource:  
//  copy&pasted on 2021-12-29.0553CST from 1st code listing here:  
/*  
https://github.com/boostorg/spirit/issues/707  
 */  
//===================  
#include <iostream>  
#include <string>  
#include <vector>  
//#define USE_TRACE_SCOPE  
#ifdef USE_TRACE_SCOPE  
#include <boost/utility/demangle_type.hpp>  
#include <boost/utility/trace_scope.hpp>  
#endif  
  
using f3_ast = std::vector<int>;  
struct S1 {  
    double   bStar;  
    uint64_t f1;  
    uint64_t f2;  
    f3_ast   f3;  
};  
  
struct S2 {  
    uint64_t f1;  
    uint64_t f2;  
    f3_ast   f3;  
};  
  
#ifdef USE_UNFOLD_LEFT  
  #include <boost/spirit/home/x3/support/ast/std_variant.hpp>  
  using f6_ast=boost::spirit::x3::std_variant<S1, S2>;  
#else  
  #include <boost/variant.hpp>  
  using f6_ast=boost::variant<S1, S2>;  
#endif//USE_UNFOLD_LEFT  
  
struct LogData {  
    int      f3;  
    int      f4;  
    uint64_t f5;  
    f6_ast   f6;  
};  
  
auto dataSet = std::vector<LogData>{};  
  
//#define MOVE_TO_SRC_LOGDATA  
#ifdef MOVE_TO_SRC_LOGDATA  
namespace boost{namespace spirit{namespace x3{namespace traits{  
    template<typename Source>  
    inline void move_to(Source& src, LogData& dest)  
    {  
        unsigned i=dataSet.size();  
        dest.f3=i;  
        std::cout<<__LINE__<<':'<<__func__<<":size="<<i<<":src="<<src<<";\n";  
    }  
}}}}  
#endif//MOVE_TO_SRC_LOGDATA  
#include <boost/fusion/adapted/struct/adapt_struct.hpp>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/traits/print_attribute.hpp>  
  
BOOST_FUSION_ADAPT_STRUCT(S1, bStar, f1, f2, f3);  
BOOST_FUSION_ADAPT_STRUCT(S2, f1, f2, f3);  
BOOST_FUSION_ADAPT_STRUCT(LogData, f3, f4, f5, f6);  
  
namespace x3 = boost::spirit::x3;  
auto f3_content  
  = x3::int_ % ','  
  ;  
auto f3_parser   
  = '{'   
  >> f3_content  
  >> '}'  
  ;  
#ifdef USE_UNFOLD_LEFT  
  #include <boost/spirit/home/x3/core/make_transform_parser.hpp>  
  #define USE_TUPLE_TIE  
  #ifdef USE_TUPLE_TIE  
    #include <boost/fusion/tuple/tuple_tie.hpp>  
  #endif//USE_TUPLE_TIE  
namespace boost{namespace spirit{namespace x3{namespace traits{  
  using f3_attr=x3::attribute_of_default<decltype(f3_parser)>;  
  
  template  
  <  
  >  
  struct  
transform_attribute  
  < f3_ast  
  , f3_attr  
  , transform_parser_id  
  >  
  {  
      using to_attribute_type=f3_ast;  
    #ifdef USE_TUPLE_TIE  
      static auto pre(to_attribute_type& to_attr)   
      { return boost::fusion::tie(unused,to_attr,unused);  
      }  
        
      template<typename From, typename To>  
      static void post(From&, To&)  
      {  //do nothing since the pre function copied this to_attr ref   
         //to the value passed to the transform_parser_attribute::from_parser.  
      }  
    #else    
      using from_attribute_type=f3_attr;  
      static auto pre(to_attribute_type& to_attr)   
      { return from_attribute_type();   
      }  
    
      static void post(to_attribute_type& to_attr, from_attribute_type& from_attr)  
      {  
          //traits::move_to(from_attr, to_attr);  
          to_attr=at_c<1>(from_attr);  
      }  
    #endif//USE_TYPLE_TIE  
  };   
}}}}  
auto f3_xform  
    = x3::make_transform_parser<f3_ast>(f3_parser)  
    ;  
#else      
auto f3_xform  
    = f3_parser  
    ;  
#endif//USE_UNFOLD_LEFT  
  
int main(int argc, char *argv[]) {  
  #ifdef USE_TRACE_SCOPE  
      boost::iostreams::indent_scoped_ostreambuf<char>  
    indent_outbuf(std::cout,2);  
  #endif  
    auto s = std::string  
    {  
R"(  39 45 13833 1.186186 2796264 221200 {13,  9,  4, 10,  2,  3}  
     34 44 45264 1.227986 2796264 884752 {14,  4,  0,  9, 11,  2}  
     44 55 66666          7777777 888888 {10, 20,  0, 30, 40, 50}  
  )"  
     };  
    if (x3::phrase_parse(  
        s.begin(), s.end(),  
        (    
            x3::int_ >> x3::int_ >> x3::uint64  
         >> (  
                (  
                    x3::double_ >> x3::uint64 >> x3::uint64  
                 >> f3_xform  
                )  
              | (  
                    x3::uint64 >> x3::uint64  
                 >> f3_xform  
                )  
            )  
        ) % x3::eol,  
        x3::blank,  
        dataSet  
    ))  
    {  
        std::cout<<"Parsing success\n";  
        for(unsigned i=0; i<dataSet.size(); ++i)  
        {  
          std::cout<<"dataSet["<<i<<"]=\n";  
          x3::traits::print_attribute(std::cout,dataSet[i]);  
          std::cout<<";\n";  
        }  
    }  
    else  
    {  
        std::cout<<"Parsing failed\n";  
    }  
}  
````

---

## Comment 10

> Username: intractabilis  
> Created at: 2022-03-27 03:39:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1079831638  

> The problem is solved by changing is_substitute.hpp as shown below:  
  
Thanks for the patch. Nikita is toxic.

---

## Comment 11

> Username: intractabilis  
> Created at: 2022-04-19 22:34:39 UTC  
> Updated at: 2022-04-19 22:35:09 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1103231416  

> The problem is solved by changing is_substitute.hpp as shown below:  
  
@cppljevans, this change breaks another perfectly legit code, see below. Any thoughts?  
```c++  
#include <iostream>  
#include <numeric>  
#include <stdexcept>  
#include <string>  
#include <vector>  
  
#include <boost/fusion/include/adapt_struct.hpp>  
#include <boost/hana/functional/fix.hpp>  
#include <boost/hana/functional/overload.hpp>  
#include <boost/spirit/home/x3.hpp>  
#include <boost/spirit/home/x3/support/ast/variant.hpp>  
  
using namespace boost::spirit;  
namespace hana = boost::hana;  
  
// Define AST. The root is `ast::expr`, which is the first left-hand side  
// operand and a list of all operations on the right-hand side. Each operand is  
// a recursive `variant` that has `ast::expr` inside.  
namespace ast  
{  
    struct nil {};  
    struct signed_;  
    struct expr;  
  
    struct operand : x3::variant<  
        nil  
      , double  
      , x3::forward_ast<signed_>  
      , x3::forward_ast<expr>  
      >  
    {  
        using base_type::base_type;  
        using base_type::operator=;  
    };  
  
    struct signed_  
    {  
        char    sign;  
        operand operand_;  
    };  
  
    struct operation  
    {  
        char    operator_;  
        operand operand_;  
    };  
  
    struct expr  
    {  
        operand                first;  
        std::vector<operation> rest;  
    };  
} // namespace ast  
  
// Give the grammar access to the fields of AST.  
BOOST_FUSION_ADAPT_STRUCT(ast::signed_, sign, operand_)  
BOOST_FUSION_ADAPT_STRUCT(ast::operation, operator_, operand_)  
BOOST_FUSION_ADAPT_STRUCT(ast::expr, first, rest)  
  
// Arithmetic expression grammar definition.  
namespace ArithExpr  
{  
    x3::rule<class expression, ast::expr   > const expression("expression");  
    x3::rule<class term,       ast::expr   > const term("term");  
    x3::rule<class factor,     ast::operand> const factor("factor");  
  
    auto const expression_def =  
        term  
     >> *(  
             (x3::char_('+') >> term)  
           | (x3::char_('-') >> term)  
         );  
    auto const term_def =  
        factor  
     >> *(  
             (x3::char_('*') >> factor)  
           | (x3::char_('/') >> factor)  
         );  
    auto const factor_def =  
        x3::double_  
     | '(' >> expression >> ')'  
     | (x3::char_('-') >> factor)  
     | (x3::char_('+') >> factor);  
  
    BOOST_SPIRIT_DEFINE(expression, term, factor);  
  
    auto calc = expression;  
} // namespace ArithExpr  
  
template <typename Iterator>  
double CalcArithExpr(Iterator const &first, Iterator last) {  
    ast::expr expr;  
    // Build AST.  
    if (!x3::phrase_parse(first, last, ArithExpr::calc, x3::ascii::space, expr)) {  
        throw std::runtime_error("Cannot parse arithmetic expression");  
    }  
  
    // Parse the AST and calculate the result.  
    // hana::fix allows recursive lambda call  
    auto astEval = hana::fix([](auto self, auto expr) -> double {  
        // hana::overload calls a lambda corresponding to the type in the variant  
        return hana::overload(  
            [](ast::nil) -> double {  
                BOOST_ASSERT(0);  
                return 0;  
            },  
            [](double x) -> double { return x; },  
            [&](ast::signed_ const &x) -> double {  
                double rhs = boost::apply_visitor(self, x.operand_);  
                switch (x.sign) {  
                    case '-': return -rhs;  
                    case '+': return +rhs;  
                }  
                BOOST_ASSERT(0);  
                return 0;  
            },  
            [&](ast::expr const &x) -> double {  
                return std::accumulate(  
                    x.rest.begin(), x.rest.end(),  
                    // evaluate recursively left-hand side  
                    boost::apply_visitor(self, x.first),  
                    [&](double lhs, const ast::operation &op) -> double {  
                        // evaluate recursively right-hand side  
                        double rhs = boost::apply_visitor(self, op.operand_);  
                        switch (op.operator_) {  
                            case '+': return lhs + rhs;  
                            case '-': return lhs - rhs;  
                            case '*': return lhs * rhs;  
                            case '/': return lhs / rhs;  
                        }  
                        BOOST_ASSERT(0);  
                        return 0;  
                    }  
                );  
            }  
        )(expr);  
    });  
  
    return astEval(expr);  
}  
  
int main(int argc, char *argv[]) {  
    auto expr = std::string{"-(4.5 + 5e-1) * 2.22 - 9.1 / 3.45"};  
    std::cout << CalcArithExpr(expr.begin(), expr.end()) << std::endl;  
}  
```  
```  
error: no matching function for call to ‘move_to(char&, ast::operation&, boost::mpl::identity<boost::spirit::x3::traits::tuple_attribute>::type)’  
```

---

## Comment 12

> Username: cppljevans  
> Created at: 2022-04-20 13:38:44 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1103944365  

On 4/19/2022 5:34 PM, intractabilis wrote:  
  
>     The problem is solved by changing is_substitute.hpp as shown below:  
>  
> @cppljevans <https://github.com/cppljevans>, this change breaks   
> another perfectly legit code, see below. Any thoughts?  
>  
> |[snip]|  
Unfortunately, I also encountered failure when trying the  
is_substitute.hpp patch on the tests here:  
  
https://github.com/boostorg/spirit/blob/develop/test/x3/alternative.cpp#L277  
https://github.com/boostorg/spirit/blob/develop/test/x3/alternative.cpp#L291  
  
I did not update the issues because I was trying, unsucessfully, to  
find a solution I could post.  I could upload the code I'm using to  
explore the problem if you'd be interested in using it to maybe also  
find a solution.  However, I'm afraid it would be more work than you'd  
be interested in doing :(  I find it hard and I wrote this exploratory  
code!  
  
Nevertheless, let me know if you'd like to have a look at this  
exploratory code.  
  
BTW, this exploratory code does have, in folder, unfold_left, an  
implementation of the non-collapsing attributes mentioned here:  
  
https://github.com/boostorg/spirit/issues/679#issuecomment-850417744  
  
This unfold_left was used in the code shown earlier:  
  
https://github.com/boostorg/spirit/issues/707#issuecomment-1005171974  
  
although, as mentioned in that comment, it's harder to adapt the  
uncollapsed attributes to fill the ast attributes.  But at least it  
compiles!!!  @Bockeman, you might be interested in this unfold_left  
code since issues/679 is the one you raised.  
  
-Larry  
  
> ||Message ID: ***@***.***>

---

## Comment 13

> Username: intractabilis  
> Created at: 2022-04-24 15:16:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1107861622  

Hi Larry,  
  
I would love to collaborate on resolving this issue. I have 20+ years of C++, so probably I can help. Yes, I would like to take a look at the exploratory code. Can I find you outside of GitHub? It would help to video chat or something to understand the issue. I tried finding you in LinkedIn, but there are too many Larry Evans.

---

## Comment 14

> Username: cppljevans  
> Created at: 2022-04-28 19:06:56 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1112560875  

intractabilis, I've just uploaded to here:  
  
https://github.com/cppljevans/boost.exploratory/tree/main/boost.additions  
  
the first part of the code you might help with.  It's sole purpose is to   
make it easier to understand  
  
compiler error messages or put trace output in code that actually runs.  
  
Please raise issues to discuss what I may have missed including in the   
uploaded code.  
  
I'll upload the boost.replacements later (that code shows actual changes   
to spirit which might  
  
contribute to solving your problem).  
  
On 4/24/2022 10:16 AM, intractabilis wrote:  
>  
> Hi Larry,  
>  
> I would love to collaborate on resolving this issue. I have 20+ years   
> of C++, so probably I can help. Yes, I would like to take a look at   
> the exploratory code. Can I find you outside of GitHub? It would help   
> to video chat or something to understand the issue. I tried finding   
> you in LinkedIn, but there are too many Larry Evans.  
>  
> —  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/spirit/issues/707#issuecomment-1107861622>,   
> or unsubscribe   
> <https://github.com/notifications/unsubscribe-auth/AAEFBA76M5VHQUS6DJJJQODVGVQVTANCNFSM5KZ5JMTQ>.  
> You are receiving this because you were mentioned.Message ID:   
> ***@***.***>  
>

---

## Comment 15

> Username: cppljevans  
> Created at: 2022-05-02 18:00:02 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1115193032  

>   
@intractabilis, I've now uploaded:  
  
  https://github.com/cppljevans/boost.exploratory/tree/main/boost.replacements/  
    
which shows how, using a no-collapsing attribute_of function, the  
attributes can exactly mirror the parsers; hence, will always succeed  
compilation without some incomprehensible compiler error messages  
occuring with the current collapsing attribute_of function.  
  
What's needed now, is to somehow, adapt the no-collapsing attribute_of  
calculated attributes to the user defined attributes.  OTOH, even  
without this, I believe it's easier for the end user to simply  
(although tediously) extract from the no-collapsing attribute_of  
calculated attributes the user defined attributes.  
  
Looking forward to your help and insight into this issue.  
  
-Larry

---

## Comment 16

> Username: cppljevans  
> Created at: 2022-07-13 21:37:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1183703124  

The boost::is_same use here:  
  
[is_substitute is_same use](https://github.com/boostorg/spirit/blob/develop/include/boost/spirit/home/x3/support/traits/is_substitute.hpp#L85)  
  
maybe the problem because it returns false when comparing:  
   attribute_of<parser_for_LogData>::type  
with LogData.  
  
Note that the attribute_of is:  
````c++  
        fusion::deque  
        < int16_t  
        , boost::variant  
          < fusion::deque  
            < double  
            , uint32_t  
            , int64_t  
            >  
          , fusion::deque  
            < uint32_t  
            , int64_t  
            >   
          >   
        >  
````  
which boost::is_same thinks is different than LogData  
although they are structurally the same.  
  
I've got a solution, I think, called is_same_enough, but it hasn't been tested  
on anything but a simplified version of your problem.  
  
Let me know if your interested in exploring further.  
  
BTW, I did join linkedIn.

---

## Comment 17

> Username: cppljevans  
> Created at: 2022-10-06 08:37:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1269591499  

The problem is caused by lack of:  
  
```c++  
        template <typename T, typename Attribute>  
        struct is_substitute_impl  
          < T  
          , Attribute  
          , typename enable_if  
            < mpl::and_  
              < is_variant<T>  
              , is_variant<Attribute>  
              , component_types_substitutable<T, Attribute>  
              >  
            >::type  
          >  
          : mpl::true_  
        {};  
```  
  
where component_types_substitutable<T, Attribute> is:  
  
```c++  
          template  
          < template<typename...>typename Aof  
          , template<typename...>typename Ast  
          , typename... Aofs  
          , typename... Asts  
          >  
          struct   
        component_types_substitutable  
          < Aof<Aofs...>  
          , Ast<Asts...>  
          >  
          {  
              static std::size_t constexpr size_Aof=  
                sizeof...(Aofs)  
                ;  
              static std::size_t constexpr size_Ast=  
                sizeof...(Asts)  
                ;  
              static bool constexpr big_enough_Ast=  
                size_Aof<=size_Ast  
                ;  
              using type=  
                mpl::and_  
                < mpl::bool_<big_enough_Ast>  
                , is_substitute  
                  < Aofs  
                  , Asts  
                  >...  
                >;  
          };  
```  
  
together with a modification of an existing is_substitute_impl from:  
  
```c++  
        template <typename T, typename Attribute>  
        struct is_substitute_impl<T, Attribute,  
            typename enable_if<  
                is_variant<Attribute>  
            >::type>  
          : variant_has_substitute<Attribute, T>  
        {};  
```  
to:  
```c++  
        template <typename T, typename Attribute>  
        struct is_substitute_impl  
          < T  
          , Attribute  
          , typename enable_if  
            < mpl::and_  
              < mpl::not_<is_variant<T>>  
              , is_variant<Attribute>  
              , variant_has_substitute<Attribute, T>  
              >  
            >::type  
          >  
          : mpl::true_  
        {};  
```  
  
Note, the movement of variant_has_substitute from superclass to  
trailing arg to mpl::and_ is insignificant and is only done to try to  
achieve more "strutureal uniformity" in code. You, AFAICT, can just use:  
  
```c++  
        template <typename T, typename Attribute>  
        struct is_substitute_impl  
          < T  
          , Attribute  
          , typename enable_if  
            < mpl::and_  
              < mpl::not_<is_variant<T>>  
              , is_variant<Attribute>  
              >  
            >::type  
          >  
          : variant_has_substitute<Attribute, T>  
        {};  
```  
  
to achieve the same result while maintaining closer similarity to  
existing code.  
  
Please see if the above solves the problem.  
  
-Larry

---

## Comment 18

> Username: cppljevans  
> Created at: 2022-10-06 09:05:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1269635549  

I should have mentioned here: https://github.com/boostorg/spirit/issues/707#issuecomment-1269591499   
that the unspecialized component_types_substitutable was:  
```c++  
          template  
          < typename Aof  
          , typename Ast  
          >  
          struct  
        component_types_substitutable  
          {  
              static std::size_t constexpr size_Aof=  
                fusion::result_of::size<Aof>::value  
                ;  
              static std::size_t constexpr size_Ast=  
                fusion::result_of::size<Ast>::value  
                ;  
              static bool constexpr big_enough_Ast=  
                size_Aof<=size_Ast  
                ;  
              using type=  
                mpl::and_  
                < mpl::bool_<big_enough_Ast>  
                , mpl::equal  
                  < Aof  
                  , Ast  
                  , is_substitute<mpl::_1, mpl::_2>  
                  >  
                >;  
          };  
```  
which was used in the "structurally similar":  
```c++  
        template <typename T, typename Attribute>  
        struct is_substitute_impl  
          < T  
          , Attribute  
          , typename enable_if  
            < mpl::and_  
              < fusion::traits::is_sequence<T>  
              , fusion::traits::is_sequence<Attribute>  
              , component_types_substitutable<T,Attribute>  
              >  
            >::type  
          >  
          : mpl::true_   
        {};  
```

---

## Comment 19

> Username: cppljevans  
> Created at: 2022-10-17 00:00:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/707#issuecomment-1280099402  

The "partial" solution is here: https://github.com/cppljevans/spirit-experiments/blob/develop/include/boost/spirit/home/x3/support/traits/is_substitute.hpp  
The test case is here: https://github.com/cppljevans/spirit-experiments/blob/develop/test/x3/issue707.cpp  
I use "partial" because it uses fold expression here: https://github.com/cppljevans/spirit-experiments/blob/develop/include/boost/spirit/home/x3/support/traits/is_substitute.hpp#L156  
and the boost test runners, by default, don't allow that, apparently.  
If you want to use it and pass the tests, you'll have to modify that #L156 code.  
Good luck, I think I've been exhausted by this and don't have any desire to continue tweaking the code.  Up to you now.
