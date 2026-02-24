# #522 - Assertion checks in ascii.hpp [Closed]

> Username: mrverhage  
> Created at: 2019-07-15 18:04:15 UTC  
> Updated at: 2019-08-26 00:37:12 UTC  
> Closed at: 2019-08-26 00:37:12 UTC  
> Url: https://github.com/boostorg/spirit/issues/522  

Hi Joel,  
  
Regarding the recent bug fix #520, Character encoding checks are now enabled. I did a checkout of the current `develop` branch to test the new functionality.  
  
As the library will now terminate with an assertion failure if a character is encountered with a value beyond 80h. I’m wondering if you still believe this behavior would make sense. Specifically I’m talking about the file ascii.hpp, which is also part of Qi. In my eyes it would make more sense to remove these assertions and just let parse fail. I also don’t get why some functions in the ascii class have the assertion check and others don’t. Like isprint has no assertion check while ispunct does.  
  
If you still believe these assertions should be there could you give some motivation for that?  
  
Regards, Zeyneb

---

## Comment 1

> Username: djowel  
> Created at: 2019-07-16 00:41:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-511619880  

> If you still believe these assertions should be there could you give some motivation for that?  
  
Agreed. It should just fail parse. Could you do a PR for this?

---

## Comment 2

> Username: mrverhage  
> Created at: 2019-07-16 00:54:27 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-511622186  

Hi Joel, I glad we share the same thought about the behavior of X3. I'm still a beginner in Github. How should I do an PR (Pull Request, right?)

---

## Comment 3

> Username: djowel  
> Created at: 2019-07-16 00:58:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-511622765  

> PR  
  
Yep. Pull request :-) Much appreciated!

---

## Comment 4

> Username: mrverhage  
> Created at: 2019-07-23 13:13:19 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-514203826  

Hi Joel,  
  
With some experimentation with edits in my local ascii.hpp file I found the following:  
Regarding the assertion statements: BOOST_ASSERT( 0 == (ch & ~UCHAR_MAX));  
for each character category function like isalpha, iscntrl. The reason why spirit was failing the assertion when ch > 0x7F is because at the start of struct ascii: char_type was typedef'ed as a (default: signed) char. So when converted to an int it got sign extended.  
  
So, believe the assertion statements should still be there but the char_type should be unsigned char instead. encoding iso8859_1 has this already but encoding standard should also be unsigned char.  
  
When char_type is unsigned char the parse just fails for characters > 0x7F. When you put in characters > 0xFF it fires the assertion.  
  
Another thing. Your standard_wide has the function prototypes like:  
static bool  
isalpha( wchar_t ch) { ... }  
  
so how about changing these functions to   
static bool  
isalpha( char_type ch ) { ... }  
for encodings: ascii, iso8859_1, standard and standard_wide?  
  
unicode has this already.  
Then I believe the assertions can be removed. Unless there are systems where UCHAR_MAX is lower than 255.  
  
Please advice.  
Best regards,  
Maarten

---

## Comment 5

> Username: mrverhage  
> Created at: 2019-07-23 13:39:31 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-514214216  

Hi Joel,  
  
Also I would like to learn how I can run the tests. I have mingw-w64 with gcc 8.1. I believe I should use test/x3/jamfile somehow?

---

## Comment 6

> Username: djowel  
> Created at: 2019-07-27 02:13:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-515643654  

Sorry I'm late. I'll need some time to review the code. Probably later today. Have you had any luck running the tests? Me, I just go to the test dir and run bjam there.

---

## Comment 7

> Username: mrverhage  
> Created at: 2019-07-27 15:03:30 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-515690758  

Hi Joel,  
  
I haven't made the edits yet and I neither have run any tests. To run the test I asked Seth/Sehe for advice. Because for me bjam isn't working.  
  
Regarding the main topic: the encoding files. I prefer to wait for your advice to what is a reasonable thing to do. You don't have to be 100% sure. But as you have designed it, you might have very good reasons why things are the way they are. I'm willing to make the edits, run the tests and prepare a pull request after that.

---

## Comment 8

> Username: djowel  
> Created at: 2019-07-29 01:52:14 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-515817305  

> Regarding the main topic: the encoding files. I prefer to wait for your advice to what is a reasonable thing to do. You don't have to be 100% sure. But as you have designed it, you might have very good reasons why things are the way they are. I'm willing to make the edits, run the tests and prepare a pull request after that.  
  
My main apprehension is that ascii is designed to work with plain char. But I think now that internally, we can use 8 bits unsigned "byte", just like we did with iso8859_1.

---

## Comment 9

> Username: mrverhage  
> Created at: 2019-08-06 15:19:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-518716694  

Hi Joel,  
  
I'm now manually making a build script to run the all the Spirit  tests. As I couldn't get the b2/bjam to work. I've asked on the Boost.Build mailing list but the responder there was not willing to address the error that was displayed to me. Or he was simply not able to say: "I don't know".  
  
Are you willing to help me a little on this? Running bjam on your system will result in a makefile right? Can you provide me the resulting makefile for at least the qi tests. Preferably for toolset=gcc?  
  
Anyway files like:  
pass_container3.cpp ;  
regression_attr_with_action.cpp ;  
regression_container_attribute.cpp ;  
regression_debug_optional.cpp : <pch>off ;  
regression_fusion_proto_spirit.cpp ;  
regression_one_element_fusion_sequence.cpp ;  
regression_one_element_sequence_attribute.cpp ;  
  
are not meant to be linked right?  
  
Best regards,  
Maarten

---

## Comment 10

> Username: cppljevans  
> Created at: 2019-08-06 17:07:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-518759334  

On 8/6/19 10:19 AM, mrverhage wrote:  
> Hi Joel,  
>   
> I'm now manually making a build script to run the all the Spirit tests.  
> As I couldn't get the b2/bjam to work. I've asked on the Boost.Build  
> mailing list but the responder there was not willing to address the  
> error that was displayed to me. Or he was simply not able to say: "I  
> don't know".  
>   
> Are you willing to help me a little on this? Running bjam on your system  
> will result in a makefile right?   
First, I confess I'm no bjam expert by my little experience shows me  
bjam does not create a makefile.  Instead, it's a make replacement.  
You should be able to just run b2 with some Jamfile.  
  
Hopefully someone else with more experience can be more helpful.  
  
-regards,  
Larry

---

## Comment 11

> Username: mrverhage  
> Created at: 2019-08-06 18:05:18 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-518780432  

Hi Larry,  
  
Although I'm making some progress with the build script in Python. I'm annoyed it didn't work for me with boost.build (bjam or b2)  
  
You contribute to Spirit sometimes do you? Are you able to run the tests? What computer/OS/toolchain do you use?  
  
Regards,  
Maarten

---

## Comment 12

> Username: cppljevans  
> Created at: 2019-08-06 18:34:47 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-518791496  

On 8/6/19 1:05 PM, mrverhage wrote:  
> Hi Larry,  
>   
> Although I'm making some progress with the build script in Python. I'm  
> annoyed it didn't work for me with boost.build (bjam or b2)  
>   
> You contribute to Spirit sometimes do you? Are you able to run the  
> tests? What computer/OS/toolchain do you use?  
>   
I use ubuntu and normally use gnu's make; however,  
I've used bjam to run the spirit tests.  
Now that I've read your post on the build mailing list,  
I remember I had a problem because using bjam requires  
that somewhere higher up in the directory hierarchy, there's  
has to be a jamroot or something like that.  The workaround  
I came up with is to create a sandbox directory underneath  
the topmost spirit directory.  Here's what it looks like  
in emacs:  
  
  /home/evansl/prog_dev/boost/releases/ro/boost_1_70_0:  
  total used in directory 136 available 407503668  
  drwxr-xr-x   9 evansl evansl  4096 Jul 24 16:16 .  
  drwxr-xr-x   4 evansl evansl  4096 Jul 24 16:17 ..  
  drwxr-xr-x 124 evansl evansl 12288 Apr  9 15:02 boost  
  -rw-r--r--   1 evansl evansl   850 Apr  9 14:35 boost-build.jam  
  -rw-r--r--   1 evansl evansl 19167 Apr  9 14:35 boostcpp.jam  
  -rw-r--r--   1 evansl evansl   989 Apr  9 14:35 boost.css  
  -rw-r--r--   1 evansl evansl  6308 Apr  9 14:35 boost.png  
  -rw-r--r--   1 evansl evansl  2997 Apr  9 14:35 bootstrap.bat  
  -rwxr-xr-x   1 evansl evansl 10807 Apr  9 14:35 bootstrap.sh  
  drwxr-xr-x   7 evansl evansl  4096 Apr  9 14:42 doc  
  -rw-r--r--   1 evansl evansl   769 Apr  9 14:35 index.htm  
  -rw-r--r--   1 evansl evansl  5567 Apr  9 15:02 index.html  
  -rw-r--r--   1 evansl evansl   291 Apr  9 14:35 INSTALL  
  -rw-r--r--   1 evansl evansl 11886 Apr  9 14:35 Jamroot  
  drwxr-xr-x 136 evansl evansl  4096 Apr  9 15:02 libs  
  -rw-r--r--   1 evansl evansl  1338 Apr  9 14:35 LICENSE_1_0.txt  
  drwxr-xr-x   4 evansl evansl  4096 Apr  9 14:35 more  
  -rw-r--r--   1 evansl evansl   541 Apr  9 14:35 README.md  
  -rw-r--r--   1 evansl evansl  2608 Apr  9 14:35 rst.css  
  drwxr-xr-x   4 evansl evansl  4096 Mar 27 03:54 sandbox  
  drwxr-xr-x   2 evansl evansl  4096 Apr  9 14:35 status  
  drwxr-xr-x  12 evansl evansl  4096 Apr  9 14:35 tools  
  
and underneath the sandbox directory, there's this:  
  
  
/home/evansl/prog_dev/boost/releases/ro/boost_1_70_0/sandbox/lje/spirit-experiments/test/x3:  
  wildcard Jam*  
  -rw-r--r-- 1 evansl evansl 3683 Feb 20 04:45 Jamfile  
  
and that Jamfile is simply a modification of the one in regular spirit.  
The modification is attached.  
  
Last time I ran from that directory, the output started out as:  
  
evansl@lje-DL:~/prog_dev/boost/releases/ro/boost_1_69_0/sandbox/lje/spirit-experiments/test/x3$  
b2 -a  
sh: 1: icpc: not found  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - Boost.Config Feature Check: cxx14_decltype_auto : yes  
    - Boost.Config Feature Check: cxx14_generic_lambdas : yes  
    - Boost.Config Feature Check: cxx14_return_type_deduction : yes  
    - Boost.Config Feature Check: cxx14_variable_templates : yes  
    - Boost.Config Feature Check: cxx14_decltype_auto : yes  
    - Boost.Config Feature Check: cxx14_generic_lambdas : yes  
    - Boost.Config Feature Check: cxx14_return_type_deduction : yes  
    - Boost.Config Feature Check: cxx14_variable_templates : yes  
...patience...  
...patience...  
...found 3200 targets...  
...updating 248 targets...  
clang-linux.compile.c++.without-pch  
../../../../../bin.v2/sandbox/lje/spirit-experiments/test/x3/x3_actions.test/clang-linux-6.0/debug/visibility-hidden/actions.o  
clang-linux.link  
../../../../../bin.v2/sandbox/lje/spirit-experiments/test/x3/x3_actions.test/clang-linux-6.0/debug/visibility-hidden/x3_actions  
.  
.  
.  
  
HTH.  
  
-Larry  
  
  
#==============================================================================  
#   Copyright (c) 2001-2013 Joel de Guzman  
#   Copyright (c) 2001-2012 Hartmut Kaiser  
#   Copyright (c)      2011 Bryce Lelbach  
#  
#   Use, modification and distribution is subject to the Boost Software  
#   License, Version 1.0. (See accompanying file LICENSE_1_0.txt or copy at  
#   http://www.boost.org/LICENSE_1_0.txt)  
#==============================================================================  
  
import config : requires ;  
import modules ;  
import feature ;  
import testing ;  
  
###############################################################################  
  
project spirit-x3  
    : requirements  
        <include>.  
        <c++-template-depth>512  
        [ requires  
            # Assume all the cxx11 checks succeed if any of cxx14 does.  
            #cxx14_binary_literals               # grep -Er "[0-9]+b[0-9]+" *  
            #cxx14_constexpr  
            cxx14_decltype_auto                 # grep -r "decltype(auto)" *  
            #cxx14_digit_separators              # grep -Er "[0-9]+'[0-9]+" *  
            cxx14_generic_lambdas               # grep -Er "]\s*\\([^\\)]*auto" *  
            #cxx14_hdr_shared_mutex              # grep -r "shared_mutex" *  
            #cxx14_initialized_lambda_captures   # grep -Er "\\[[^=\\]]+=" *  
            #cxx14_aggregate_nsdmi  
            cxx14_return_type_deduction         # grep -Er "auto[^\\(=\\)]+\(" *  
            #cxx14_std_exchange                  # grep -r "exchange" *  
            cxx14_variable_templates  
        ]  
    ;  
  
###############################################################################  
  
local subproject-name = x3 ;  
  
rule run ( sources + : args * : input-files *  
         : requirements * : target-name ? : default-build * )  
{  
    target-name ?= $(subproject-name)_$(sources[1]:D=:S=) ;  
    return [ testing.run $(sources) : $(args) : $(input-files)  
           : $(requirements) : $(target-name) : $(default-build) ] ;  
}  
  
rule compile ( sources + : requirements * : target-name ? )  
{  
    target-name ?= $(subproject-name)_$(sources[1]:D=:S=) ;  
    return [ testing.compile $(sources)  
           : $(requirements) : $(target-name) ] ;  
}  
  
rule compile-fail ( sources + : requirements * : target-name ? )  
{  
    target-name ?= $(subproject-name)_$(sources[1]:D=:S=) ;  
    return [ testing.compile-fail $(sources)  
           : $(requirements) : $(target-name) ] ;  
}  
  
###############################################################################  
  
run actions.cpp ;  
run alternative.cpp ;  
run and_predicate.cpp ;  
run any_parser.cpp ;  
run attr.cpp ;  
run binary.cpp ;  
run bool.cpp ;  
run char1.cpp ;  
run char_class.cpp ;  
run container_support.cpp ;  
run debug.cpp ;  
run difference.cpp ;  
run eoi.cpp ;  
run eol.cpp ;  
run eps.cpp ;  
run expect.cpp ;  
run extract_int.cpp ;  
run int1.cpp ;  
run kleene.cpp ;  
run lexeme.cpp ;  
run lit1.cpp ;  
run lit2.cpp ;  
run list.cpp ;  
run matches.cpp ;  
run no_case.cpp ;  
run no_skip.cpp ;  
run not_predicate.cpp ;  
run omit.cpp ;  
run optional.cpp ;  
run plus.cpp ;  
run with.cpp ;  
  
run raw.cpp ;  
run real1.cpp ;  
run real2.cpp ;  
run real3.cpp ;  
run real4.cpp ;  
run rule1.cpp ;  
run rule2.cpp ;  
run rule3.cpp ;  
run rule4.cpp ;  
run sequence.cpp ;  
run skip.cpp ;  
run symbols1.cpp ;  
run symbols2.cpp ;  
run symbols3.cpp ;  
run tst.cpp ;  
  
run uint1.cpp ;  
run uint_radix.cpp ;  
  
run confix.cpp ;  
run repeat.cpp ;  
run seek.cpp ;  
  
run attribute_type_check.cpp ;  
run fusion_map.cpp ;  
run x3_variant.cpp ;  
run error_handler.cpp /boost//filesystem ;  
run iterator_check.cpp ;  
  
run to_utf8.cpp ;  
  
obj rule_separate_tu_grammar : rule_separate_tu_grammar.cpp ;  
run rule_separate_tu.cpp rule_separate_tu_grammar ;  
  
run only_1_xform.cpp ;

---

## Comment 13

> Username: cppljevans  
> Created at: 2019-08-06 18:49:29 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-518796585  

On 8/6/19 1:34 PM, cppljevans wrote:  
> On 8/6/19 1:05 PM, mrverhage wrote:  
>> Hi Larry,  
>>  
>> Although I'm making some progress with the build script in Python. I'm  
>> annoyed it didn't work for me with boost.build (bjam or b2)  
>>  
>> You contribute to Spirit sometimes do you? Are you able to run the  
>> tests? What computer/OS/toolchain do you use?  
>>  
> I use ubuntu and normally use gnu's make; however,  
> I've used bjam to run the spirit tests[snip]  
I should also have mentioned that the spirit-experiments directory  
is a github clone of spirit.  And now that I've thought a little  
more about it, I realize there must be some way to cause the  
spirit-experiments to be included before the one from from  
boost_1_70_0; however, I don't remember how that was done.  
Sorry.  
  
-Larry

---

## Comment 14

> Username: cppljevans  
> Created at: 2019-08-06 19:23:01 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-518808323  

On 8/6/19 1:49 PM, cppljevans wrote:  
> On 8/6/19 1:34 PM, cppljevans wrote:  
>> On 8/6/19 1:05 PM, mrverhage wrote:  
>>> Hi Larry,  
>>>  
>>> Although I'm making some progress with the build script in Python. I'm  
>>> annoyed it didn't work for me with boost.build (bjam or b2)  
>>>  
>>> You contribute to Spirit sometimes do you? Are you able to run the  
>>> tests? What computer/OS/toolchain do you use?  
>>>  
>> I use ubuntu and normally use gnu's make; however,  
>> I've used bjam to run the spirit tests[snip]  
> I should also have mentioned that the spirit-experiments directory  
> is a github clone of spirit. And now that I've thought a little  
> more about it, I realize there must be some way to cause the  
> spirit-experiments to be included before the one from from  
> boost_1_70_0; however, I don't remember how that was done.  
> Sorry.  
>   
It (the inclusion of my spirit-experiments in from of boost_1_70_0  
spirit) may have been done by modifying the user-config.jam file  
so that the compile command does the inclusions for spirit-experiments  
first.  IOW, something like:  
  
local LJE_INC =  
/home/evansl/prog_dev/boost/releases/ro/boost_1_70_0/sandbox/lje/spirit-experiments/include  
;  
.  
.  
.  
using clang  
  : 6.0  
  : "/usr/bin/clang++"  
  : <cxxflags>-std=c++1z  
    <cxxflags>-I$(LJE_INC)  
  ;  
  
where, of course, LJE_INC would be defined to include your github clone.  
  
-regards,  
Larry

---

## Comment 15

> Username: djowel  
> Created at: 2019-08-06 22:47:21 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-518871681  

I'm hoping you can make bjam work. It's a must for Boost development. Are you able to build Boost in the first place?

---

## Comment 16

> Username: mrverhage  
> Created at: 2019-08-07 15:44:54 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-519154969  

Hi Joel,  
  
Sure I was able to build boost. No problems. Are you willing to increase your faith in my abilities and judgement?  
  
Obviously when I'm not able to build boost I'm going to solve that first. Before talking about contributing to Spirit.  
  
As for the build script that I'm making in Python, I'm making good progress. The Python script in turn generate a gcc makefile to build all of the tests in Spirit. I already got all of Qi/Lex/Karma/Support  to work with No errors detected for each file. But I don't know if details like using PCH affect the validity of the tests.  
  
As for running the Spirit tests with Boost.Build. I would need help from a knowledgeable Boost member who is willing to sort things out. My question is still on the Boost.Build mailing list. I did have a bad experience with Edward Diener. But maybe you or your coworker Michael can assist in finding a Boost member to resolve this problem. After all my goal is to make a valuable contribution to Spirit.  
  
Regards, Maarten

---

## Comment 17

> Username: djowel  
> Created at: 2019-08-07 21:56:28 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-519283834  

Running the test is as simple as CD'ing to the test directory and running b2 or bjam there (assuming bjam is in the path). Out of curiosity, although I fear we're going off topic here, what was your question (sorry I am not following Boost.Build list)?

---

## Comment 18

> Username: cppljevans  
> Created at: 2019-08-09 14:17:24 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-519936478  

On 8/7/19 4:56 PM, Joel de Guzman wrote:  
> Running the test is as simple as CD'ing to the test directory and  
> running b2 or bjam there (assuming bjam is in the path). Out of  
> curiosity, although I fear we're going off topic here, what was your  
> question (sorry I am not following Boost.Build list)?  
>   
Here's a link to the build post:  
  
https://lists.boost.org/boost-build/2019/08/30231.php  
  
In short, when trying to use b2 to run the spirit tests,  
Maarten is getting some error message about  
missing config.jam file.  
  
I've no idea what's causing it; hence, I can't help :(  
  
-regards,  
Larry

---

## Comment 19

> Username: djowel  
> Created at: 2019-08-10 01:30:45 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-520107048  

> I've no idea what's causing it; hence, I can't help :(  
  
I have no idea too :-( First time I've seen that error. I do test on windows, but typically using MSVC.

---

## Comment 20

> Username: mrverhage  
> Created at: 2019-08-12 18:50:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-520549641  

Hi Joel,  
  
Ok, I succeeded in running all the Spirit tests of the current develop version. With my own Python script that builds the gcc makefile. All tests completed successfully. I am also able to produce multiple makefiles that have different compile flags which are applied to all the test files.   
  
Back to char_encodings. First thing I did was to edit ascii.hpp to have:  
```cpp  
typedef unsigned char char_type;  
```  
instead of the plain char one that it currently has.  
  
This worked with an example where I did pass references to an uint8_t  in the parse function 1st and 2nd argument. Then I run the tests and it starts to fail at list.cpp line 76:  
```  
move_to.hpp:194:24: error: no matching function for call to 'move_to(std::remove_reference<const char&>::type, std::__cxx11::basic_string<char>&, boost::mpl::identity<boost::spirit::x3::traits::container_attribute>::type)'  
```  
For encoding iso8859_1 you also use unsigned char as the char_type. So I made a little test case program to see if iso8859_1 would produce the same compile error and it did. If you switch back to standard encoding the program compiles and runs fine. The question is do you consider my test case program below valid? If yes, I believe the issue about move_to has to be resolved first. And I believe it would be a good idea to add tests for iso8859_1 encoding as well. I hope you're willing to give this a priority, it goes a bit over my head.  
  
```cpp  
#include <cstddef>  
#include <cstdio>  
#include <cstdint>  
  
#include <boost/spirit/home/x3.hpp>  
namespace x3 = boost::spirit::x3;  
  
//using namespace x3::iso8859_1;  
using namespace x3::standard;  
  
int main()  
{  
  std::vector<std::string> v;  
  bool r;  
  const char input[] = "a,b,c,d";  
    
  char const* in = input;  
  char const* last = input;  
  while (*last) { last++; }  
    
  r = parse( in, last, +alpha % lit(','), v );  
  if ( r )  
  {  
    printf( "parsing succeded\n" );  
  
    for ( const std::string& str : v )  
    {  
      printf( "%s\n", str.c_str() );  
    }  
  }  
  else  
  {  
    printf( "parsing failed\n" );  
  }  
  return 0;  
}  
```  
Below is the full compiler spew or maybe vomit is more applicable as it is 36KB.  
[result.txt](https://github.com/boostorg/spirit/files/3493863/result.txt)

---

## Comment 21

> Username: djowel  
> Created at: 2019-08-14 06:43:03 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-521122279  

I'll look into it as soon as I can.

---

## Comment 22

> Username: djowel  
> Created at: 2019-08-26 00:27:46 UTC  
> Url: https://github.com/boostorg/spirit/issues/522#issuecomment-524677468  

The asserts are now removed. Please see the following commits:  
  
- https://github.com/boostorg/spirit/commit/dd2731ffa17b01c8c83fec18f57dcd5059971af7  
- https://github.com/boostorg/spirit/commit/730b35be1fd97d1508354c06024226d27d0d4ce6  
- https://github.com/boostorg/spirit/commit/a27e00cad9bb312ea828e61185a9ca0029259376  
  
Please check if these works for you. Thanks!
