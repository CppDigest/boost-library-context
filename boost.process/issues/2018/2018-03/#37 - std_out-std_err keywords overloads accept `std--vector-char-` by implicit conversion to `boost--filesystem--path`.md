# #37 - std_out/std_err keywords overloads accept `std::vector<char>` by implicit conversion to `boost::filesystem::path` [Open]

> Username: sehe  
> Created at: 2018-03-25 01:17:30 UTC  
> Updated at: 2018-03-27 03:09:21 UTC  
> Url: https://github.com/boostorg/process/issues/37  

std_out/std_err keywords overloads accept `std::vector<char>` by implicit conversion to `boost::filesystem::path`.  
  
This is completely surprising, because  
  
      std::vector<char> output;  
      bp::child(command, bp::std_out > output);  
  
compiles and will be expected to do the equivalent of  
  
      std::future<std::vector<char>> output;  
      boost::asio::io_service io;  
      bp::child(command, bp::std_out > output, io);  
       
However, hard to diagnose errors arise.   
  
> A secondary issue might be that if the `dup2` call fails (which it does) that **doesn't stop de child process from running**. This has lead to me not diagnosing this exact error for 3 weeks¹, until I finally noticed my oversight²  
  
¹ due to this SO answer: https://stackoverflow.com/questions/49139875/boostprocess-with-standard-output-redirection-fails-randomly-with-ubuntu-16/49140495  
  
² due to making the same error in this SO answer https://stackoverflow.com/questions/49462992/how-to-test-an-instance-counter-by-asynchronous-run-of-a-boost-childprocess  
  
## Reason  
  
`std::vector<char>` converts to `path` implicitly:  
  
    std::vector<char> processOutput;  
    boost::filesystem::path p = processOutput;  
  
We might want to rule out implicit conversions by going through a dispatch template.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2018-03-26 16:54:22 UTC  
> Updated at: 2018-03-26 16:56:43 UTC  
> Url: https://github.com/boostorg/process/issues/37#issuecomment-376235658  

First of all: your answering on SO is amazing, thanks for that!  
Secondly: you mean I should add an explicitly deleted version of `std_out > vector<char>` ?  
  
I see that this is somewhat of a useless overload, tbh, but your given example is covered by:  
  
```cpp  
  std::vector<char> output;  
  bp::child(command, bp::std_out > bp::buffer(output));  
```  
  
and, actually is documented.

---

## Comment 2

> Username: sehe  
> Created at: 2018-03-26 23:25:25 UTC  
> Url: https://github.com/boostorg/process/issues/37#issuecomment-376344405  

I'm aware of the `asio::buffer` facilities. I'm in no way suggesting a documentation issue.  
  
This issue is about the [completely undocumentable] pitfall that passing a `std::vector<char>` will be treated as a `path`.  
  
Rather than special casing it (in a while we'll notice another surprising overload resolution), I'd like to add a dispatch that makes sure that no implicit conversion is taken. This could even be used defensively in other keyword expression templates.

---

## Comment 3

> Username: klemens-morgenstern  
> Created at: 2018-03-27 03:04:57 UTC  
> Updated at: 2018-03-27 03:09:21 UTC  
> Url: https://github.com/boostorg/process/issues/37#issuecomment-376381520  

Ok, so you think of something like that:  
  
```cpp  
struct std_out_  
{  
    //..other stuff  
   foo operator>(const fs::path& ); //does what you think  
   template<typename T>  
   foo operator>(const T &) = delete;  
};  
```  
  
I am not sure that's a good idea, even though I acknowledge it's a pitfall. The reason is, that you might (it's a DSEL after all) want to some more obscure stuff, I could not forsee, e..g use a `string_view` that is implicitly convertible to string. That would HAVE to work out of the box, which is why we have implicit conversions. But maybe I am misunderstanding what you propose.  
  
**EDIT**: Yeah I think that's bad design on the part of `fs::path` http://www.boost.org/doc/libs/1_66_0/libs/filesystem/doc/reference.html#path-constructors
