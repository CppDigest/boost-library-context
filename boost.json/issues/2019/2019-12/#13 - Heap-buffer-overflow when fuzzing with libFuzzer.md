# #13 - Heap-buffer-overflow when fuzzing with libFuzzer [Closed]

> Username: msimonsson  
> Created at: 2019-12-02 09:29:38 UTC  
> Updated at: 2020-05-02 01:06:49 UTC  
> Closed at: 2020-05-02 01:06:49 UTC  
> Url: https://github.com/boostorg/json/issues/13  

Fuzzing the validate() function in example/validate.cpp results in heap-buffer-overflow:  
```  
==75832==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x603000005901 at pc 0x00000031fa46 bp 0x7fffffff8f50 sp 0x7fffffff8f48  
READ of size 1 at 0x603000005901 thread T0  
    #0 0x31fa45 in boost::json::basic_parser::write_some(char const*, unsigned long, std::__1::error_code&)  
        boost/json/impl/basic_parser.ipp:456:16  
    #1 0x30f73c in boost::json::basic_parser::write(char const*, unsigned long, std::__1::error_code&)  
        boost/json/impl/basic_parser.ipp:894:9  
    #2 0x309cf2 in boost::json::basic_parser::finish(char const*, unsigned long, std::__1::error_code&)  
        boost/json/impl/basic_parser.ipp:924:5  
    #3 0x30937e in (anonymous namespace)::validate(std::__1::basic_string_view<char, std::__1::char_traits<char> >)  
        bjson.cc:73:11  
```  
  
Input:  
```  
\"~QQ36644632   {n  
Base64: In5RUTM2NjQ0NjMyICAge24=  
```  
  
Please let me know if you need more details.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-12-02 11:16:51 UTC  
> Url: https://github.com/boostorg/json/issues/13#issuecomment-560352130  

"That which is untested, contains bugs."  
  
I'm really skeptical of an error in the JSON library because I do run it under all the sanitizers. What is interesting is that this looks like a bug in the example code, I suspect my implementation of `read_file` is broken (LOL).  
  
Does the problem go away if you change the read mode to binary (`"rb"`) here?  
https://github.com/vinniefalco/json/blob/develop/example/file.hpp#L138

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-12-02 11:20:14 UTC  
> Url: https://github.com/boostorg/json/issues/13#issuecomment-560353435  

I added this test  
```  
    void  
    testIssue13()  
    {  
        validate(  
"\"~QQ36644632   {n\n"  
"Base64: In5RUTM2NjQ0NjMyICAge24="  
        );  
    }  
```  
  
But I suspect that it doesn't malfunction.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-12-02 12:21:36 UTC  
> Url: https://github.com/boostorg/json/issues/13#issuecomment-560373354  

As I suspected, the test passes asan:  
https://travis-ci.org/vinniefalco/json/jobs/619576059  
  
Can you please provide the program and command line you used to fuzz the validate function?  
  
Thanks!

---

## Comment 4

> Username: msimonsson  
> Created at: 2019-12-02 12:45:32 UTC  
> Url: https://github.com/boostorg/json/issues/13#issuecomment-560381424  

Sure, here's the program (bjson.cc), I removed all <boost/config.hpp> includes in the library:  
```  
#define BOOST_FALLTHROUGH [[fallthrough]]  
#include "boost/json.hpp"  
  
namespace  
{  
    bool  
    validate( std::string_view s )  
    {  
        using namespace boost::json;  
  
        // The null parser discards all the data  
  
        struct null_parser : basic_parser  
        {  
            null_parser() {}  
            ~null_parser() {}  
            void on_document_begin( error_code& ) override {}  
            void on_document_end( error_code& ) override {}  
            void on_object_begin( error_code& ) override {}  
            void on_object_end( error_code& ) override {}  
            void on_array_begin( error_code& ) override {}  
            void on_array_end( error_code& ) override {}  
            void on_key_part( string_view, error_code& ) override {}  
            void on_key( string_view, error_code& ) override {}  
            void on_string_part( string_view, error_code& ) override {}  
            void on_string( string_view, error_code& ) override {}  
            void on_int64( std::int64_t, error_code& ) override {}  
            void on_uint64( std::uint64_t, error_code& ) override {}  
            void on_double( double, error_code& ) override {}  
            void on_bool( bool, error_code& ) override {}  
            void on_null( error_code& ) override {}  
        };  
  
        // Parse with the null parser and return false on error  
        null_parser p;  
        error_code ec;  
        p.finish( s.data(), s.size(), ec );  
        if( ec )  
            return false;  
  
        // The string is valid JSON.  
        return true;  
    }  
}  
  
extern "C" int LLVMFuzzerTestOneInput(const uint8_t* data, size_t size);  
extern "C" int LLVMFuzzerTestOneInput(const uint8_t* data, size_t size)  
{  
    validate(std::string_view{reinterpret_cast<const char*>(data), size});  
    return 0;  
}  
```  
Compile with:  
`clang++90 --config ./.clang -fsanitize=fuzzer,address,undefined -fno-sanitize-recover=undefined -DBOOST_JSON_STANDALONE -DBOOST_JSON_HEADER_ONLY -I../ -o bjson bjson.cc`  
  
Here's my .clang config:  
```  
-std=c++2a  
-stdlib=libc++  
-march=x86-64  
  
-isystem /usr/local/include/  
  
-ferror-limit=2  
-Werror=zero-length-array  
  
-Weverything  
-Wno-c++98-compat  
-Wno-c++98-compat-pedantic  
-Wno-ctad-maybe-unsupported  
-Wno-padded  
-Wno-return-std-move-in-c++11  
-Wno-shadow-uncaptured-local  
-Wno-switch-enum  
-Wno-undefined-func-template  
-Wno-weak-vtables  
```  
  
Fuzz:  
```  
mkdir bjson.corpus  
./bjson bjson.corpus/  
```  
  
Environment:  
```                                                                                                                      
% clang++90 --version  
clang version 9.0.0 (tags/RELEASE_900/final)  
Target: x86_64-portbld-freebsd12.0  
Thread model: posix  
InstalledDir: /usr/local/llvm90/bin  
```  
  
P.S. The test should be (if a base64_decode() function is available):  
```  
    void  
    testIssue13()  
    {  
        validate(  
base64_decode("In5RUTM2NjQ0NjMyICAge24=")  
        );  
    }  
```

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-12-02 13:30:06 UTC  
> Url: https://github.com/boostorg/json/issues/13#issuecomment-560397124  

I fixed the boost/config.hpp thing in the tip of _develop_

---

## Comment 6

> Username: vinniefalco  
> Created at: 2019-12-02 15:18:19 UTC  
> Url: https://github.com/boostorg/json/issues/13#issuecomment-560440864  

hmm.. your `LLVMFuzzerTestOneInput` doesn't even go through `read_file`...

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-12-02 17:02:51 UTC  
> Url: https://github.com/boostorg/json/issues/13#issuecomment-560486072  

I believe this commit fixes the problem:  
https://github.com/vinniefalco/json/commit/c08721906d67c17eb8bda679550ca7710856d917  
  
However, I am waiting for someone to help me integrate fuzzing to a GitHub action in order to make it part of the CI setup.

---

## Comment 8

> Username: msimonsson  
> Created at: 2019-12-02 18:19:43 UTC  
> Updated at: 2019-12-02 18:20:15 UTC  
> Url: https://github.com/boostorg/json/issues/13#issuecomment-560517119  

That fixed it.  
  
Here's a less serious issue, this input triggers an assert:  
  
> [[],0.000000000000000000100,0.00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000  
  
`Assertion failed: (exp >= 0 && exp < 618), function pow10, file ../boost/json/detail/number.ipp, line 96.`  
  
I'm not going to fuzz any further, integrating fuzzing as a part of CI sounds great.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-12-02 18:20:21 UTC  
> Url: https://github.com/boostorg/json/issues/13#issuecomment-560517350  

Thanks for doing this!!

---

## Comment 10

> Username: pauldreik  
> Created at: 2019-12-03 20:55:48 UTC  
> Url: https://github.com/boostorg/json/issues/13#issuecomment-561352991  

I found a new crash while writing the fuzzer script, do you want me to file a separate issue?  
The input is (base64):  
```  
WyL//34zOVx1ZDg0ZFx1ZGM4M2RcdWQ4M2RcdWRlM2M4dWRlMTlcdWQ4M2RcdWRlMzlkZWUzOVx1  
ZDg0ZFx1ZGM4M2RcdWQ4M2RcdWRlMzlcXHVkY2M4M1x1ZDg5ZFx1ZGUzOVx1ZDgzZFx1ZGUzOWRb  
IGZhbHNlLDMzMzMzMzMzMzMzMzMzMzMzNDMzMzMzMTY1MzczNzMwLDMzMzMzMzMzMzMzMzMzMzMz  
MzM3ODAsMzMzMzMzMzMzMzM0MzMzMzMxNjUzNzM3MzAsMzMzMzMzMzMzMzMzMzMzMzMzMzMzMzMz  
MzM3ODAsMzMzMzMzMzMzMzMzMzQzMzMzMzE2NTM3MzczMCwzMzMzMzMzMzMzMzMzMzMzMzMzNzgw  
LDMzMzMzMzM4MzU1MzMwNzQ3NDYwLDMzMTY2NTAwMDAzMzMzMzMwNzQ3MzMzMzMzMzc3OSwzMzMz  
MzMzMzMzMzMzMzMzNDMzMzMzMzMwNzQ3NDYwLDMzMzMzMzMzMzMzMzMzMzMzMzMzNzgwLDMzMzMz  
MzMzMzMzMzMzMzA4ODM1NTMzMDc0Mzc4MCwzMzMzMzMzMzMzMzMzMzMwODgzNTUzMzA3NDc0NjAs  
MzMzMzMzMzMxNjY1MDAwMDMzMzMzNDc0NjAsMzMzMzMzMzMzMzMzMzMzMzMzMzc4MCwzMzMzMzMz  
MzMzMzM3MzMzMzE2NjUwMDAwMzMzMzMzMDc0NzMzMzMzMzM3NzksMzMzMzMzMzMzMzMzMzMzMzQz  
MzMzMzMwNzQ3NDYwLDMzMzMzMzMzMzMzMzMzMzMzMzMzMzMzMzMzMzc4MCwzMzMzMzMzMzMzNzgw  
LDMzMzMzMzMzMzMzMzA4ODM1NTMzMDc0NzQ2MCwzMzE2NjUwMDAwMzMzMzMzMDc0NzMzMzMzMzM3  
NzksMzMzMzMzMzMzMzMzMzMzMzQzMzMzMzMwNzQ3NDYwLDMzMzMzMzMzMzMzMzMzMzMzMzMzNzgw  
LDMzMzMzMzMzMzMzMzMzMzA4ODM1NTMzMDc0Mzc4MCwzMzMzMzMzMzMzMzMzMzMzMzMwODgzNTUz  
MzA3NDM3ODAsMzMzMzMzMzMzMzMzMzMzMDg4MzU1MzMwNzQ3NDYwLDMzMzMzMzMzMzMzMDczMzM3  
NDc0NjAsMzMzMzMzMzMzMzMzMzMzMzMzNzgwLDMzMzMzMzMzMzMzMzA4ODM1NTMzMDc0NzQ2MCwz  
MzE2NjUwMDAwMzMzMzMzMDc0NzMzMzMzMzM3NzksMzMzMzMzMzMzMzMzMzMzMzQzMzMzMzMzMDc0  
NzQ2MCwzMzMzMzMzMzMzMzMzMzMzMzMzMzMzMzMzMzMzMzM3ODAsMzMzMzMzMzMzMzMzMzMzMDg4  
MzU1MzMwNzQzNzgwLDMzMzMzMzMzMzMzMzMzMzA4ODM1NTMzMDc0NzQ2MCwzMzMzMzMzMzMzMzMz  
MzMzMzM0MjQ3LDMzMzMzMzMzMzMzMzMzMzQzMzMzMzMzMzMzMzMzMzM3MzMzMzQzMzMzMzMzMDc0  
NzQ2MCwzMzMzMzMzMzMzMzMzMzMzMzMzNzgwLDMzMzMzMzMzMzMzMzA4ODM1NTMzMDc0NzQ2MCwz  
MzE2NjUwMDAwMzMzMzMzMDc0NzMzMzMzMzM3NzksMzMzMzMzMzMzMzMzMzMzMzQzMzMzMzMwNzQ3  
NDYwLDMzMzMzMzMzMzMzMzMzMzMzMzMzNzgwLDMzMzMzMzMzMzMzMzMzMzA4ODM1NTMzMDc0Mzc4  
MCwzMzMzMzMzMzMzMzMzMzMwODgzNTUzMzA3NDc0NjAsMzMzMzMzMzMzLDMzMzMzMzMzMzMzMzMz  
MzMzMzM3ODAsMzMzMzMzMzMzMzc4MCwzMzMzMzMzMzMzMzMwODgzNTUzMzA3NDc0NjAsMzMxNjY1  
MDAwMDMzMzMzMzA3NDczMzMzMzMzNzc5LDMzMzMzMzMzMzM3ODAsMzMzMzMzMzgzNTUzMzA3NDc0  
NjAsMzMxNjY1MDAwMDMzMzMzMzA3NDczMzMzMzMzNzc5LDMzMzMzMzMzMzMzMzMzMzM0MzMzMzMz  
MzA3NDc0NjAsMzMzMzMzMzMzMzMzMzMzMzMzMzM3ODAsMzMzMzMzMzMzMzMzMzMzMDg4MzU1MzMw  
NzQzNzgwLDMzMzMzMzMzMzMzMzMzMzA4ODM1NTMzMDc0NzQ2MCwzMzMzMzMzMzE2NjUwMDAwMzMz  
MzM0NzQ2MCwzMzMzMzMzMzMzMzMzMzMzMzMzNzgwLDMzMzMzMzMzMzMzMzM0MzMzMzMxNjUzNzM3  
MzAsMzMzMzMzMzMzMzMzMzMzMzMzMzc4MCwzMzMzMzMzODM1NTMzMDc0NzQ2MCwzMzE2NjUwMDAw  
MzMzMzMzMDc0NzMzMzMzMzM3NzksMzMzMzMzMzMzMzMzMzMzMzQzMzMzMzMzMDc0NzQ2MCwzMzMz  
MzMzMzMzMzMzMzMzMzMzMzc4MCwzMzMzMzMzMzMzMzMzMzMwODgzNTUzMzA3NDM3ODAsMzMzMzMz  
MzMzMzMzMzMzMDg4MzU1MzMwNzQ3NDYwLDMzMzMzMzMzMTY2NTAwMDAzMzMzMzQ3NDYwLDMzMzMz  
MzMzMzMzMzMzMzMzMzM3ODAsMzMzMzMzMzMzMzMzNzMzMzM0MzMzMzMzMzA3NDc0NjAsMzMzMzMz  
MzMzMzMzMzMzMzMzMzc4MCwzMzMzMzMzMzMzMzMwODgzNTUzMzA3NDc0NjAsMzMxNjY1MDAwMDMz  
MzMzMzA3NDczMzMzMzMzNzc5LDMzMzMzMzMzMzMzMzMzMzM0MzMzMzNcdWQ4N2RcdWRlZGV1ZGM4  
ZGUzOVx1ZDg0ZFx1ZGM4M2RcdWQ4OGRcdWRlMzlcdWQ4OWRcdWRlMjM5MzMzZWUzOVxk  
```  
  
And the crash is  (exact number on index varies)  
```  
../include/boost/json/detail/buffer.hpp:70:9: runtime error: index 41884 out of bounds for type 'char [2048]'  
```

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-12-04 01:39:57 UTC  
> Url: https://github.com/boostorg/json/issues/13#issuecomment-561436000  

Hey that's pretty cool :)  
  
I am investigating...

---

## Comment 12

> Username: boris-kolpackov  
> Created at: 2019-12-05 11:22:21 UTC  
> Url: https://github.com/boostorg/json/issues/13#issuecomment-562088085  

BTW, another good thing to try is tests from [JSONTestSuite](https://github.com/nst/JSONTestSuite) as an initial corpus (i.e., copy `tests_parsing/*` and potentially `tests_transform/*` into `bjson.corpus/`). See the [LibFuzzer](https://llvm.org/docs/LibFuzzer.html) docs for details on the corpus seeding.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2020-05-02 01:06:49 UTC  
> Url: https://github.com/boostorg/json/issues/13#issuecomment-622646508  

This should all be fixed now. If there are still problems, please open a new issue - thanks!
