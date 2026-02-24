# #127 - boost::program_options::parse_command_line crashes with argc of ISO Cpp valid value of 0 [Closed]

> Username: Superlokkus  
> Created at: 2023-11-30 15:11:49 UTC  
> Updated at: 2024-04-01 10:58:56 UTC  
> Closed at: 2024-04-01 10:58:55 UTC  
> Url: https://github.com/boostorg/program_options/issues/127  

`boost::program_options::parse_command_line(argc, argv, parameters)` invokes undefined behavior and crashes with a value of argc of 0 (and argv nullptr).   
  
These values for argc and argv are perfectly valid according to   
  
[6.9.3.1 main function[basic.start.main]](https://eel.is/c++draft/basic.start.main#2.2)  
  
> The value of argc shall be non-negative[.](https://eel.is/c++draft/basic.start.main#2.sentence-6) The value of argv[argc] shall be 0[.](https://eel.is/c++draft/basic.start.main#2.sentence-7)  
  
(Of course this assumes 0 is non-negative, but the cpp standard seems to assume this too as it states „If argc is nonzero these arguments shall…“).  
  
To be fair, almost none implementation might uses 0, when writing an integration test, as I did, that surrogates argc and argv, as any regular parameter for this boost function, I would expect no UB, when not violating any API contract.  
  
[Looking at the code this is no suprise as](https://github.com/boostorg/program_options/blob/develop/include/boost/program_options/detail/parsers.hpp#L27C23-L27C45)  
  
```  
basic_command_line_parser(int argc, const charT* const argv[])  
    : detail::cmdline(  
        to_internal(std::vector<std::basic_string<charT> >(argv+1, argv+argc))),  
  
```  
  
uses the `std::vector(begin,end)`  constructor, however with argc of 0, end will be < than begin, hence undefined behaviour and luckily an exception.  
  
Older boost code did righteously check for `argc == 0`  
  
```  
   basic_command_line_parser(int argc, const charT* const argv[])  
    : detail::cmdline(  
        to_internal(std::vector<std::basic_string<charT> >(  
                      // When argc == 0, we must produce a valid empty range,  
                      // even with a nullptr argv.  We can ignore the  
                      // obviously illegal argc < 0.  
                      argc ? argv+1 : argv, argv+argc))),  
  
```  
  
  
[The following code ](https://compiler-explorer.com/z/o3zzcr3P6)  
  
```  
  
#include <boost/program_options.hpp>  
  
  
int main() {  
    boost::program_options::options_description parameters("Parameters");  
    int argc = 0; //Allowed value see https://eel.is/c++draft/basic.start.main#2.2  
    char* argv[] {nullptr};  
    boost::program_options::parse_command_line(argc, argv, parameters);  
}  
```  
  
crashes, luckily with an exception from `std::vector`

---

## Comment 1

> Username: vprus  
> Created at: 2023-11-30 16:21:56 UTC  
> Url: https://github.com/boostorg/program_options/issues/127#issuecomment-1834098496  

Thanks for the bug report.  
  
Unfortunately, I won't be able to personally fix it in foreseeable future, because  
- I don't have much time these days, and  
- While the standard might say that argc==0 is valid, nobody else complained in a dozen years  
  
I can leave the issue in the dormant state, or I'll be happy to approve PR if you have time for that.  
  
Thanks!

---

## Comment 2

> Username: yihuajack  
> Created at: 2024-03-27 10:39:23 UTC  
> Url: https://github.com/boostorg/program_options/issues/127#issuecomment-2022438647  

Since PR#128 has fixed this issue, maybe this issue could be marked as closed?

---

## Comment 3

> Username: vprus  
> Created at: 2024-04-01 10:58:55 UTC  
> Url: https://github.com/boostorg/program_options/issues/127#issuecomment-2029582803  

Yes, thanks.
