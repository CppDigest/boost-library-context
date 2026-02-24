# #124 - Peeking into std::cin through a boost::iostreams::filtering_istream forever blocks [Open]

> Username: yobeonline  
> Created at: 2021-01-25 09:57:32 UTC  
> Updated at: 2022-02-01 21:56:52 UTC  
> Url: https://github.com/boostorg/iostreams/issues/124  

In the below code, I am wrapping `std::cin` into a `boost::iostreams::filtering_istream`. Calling `peek()` on the resulting object seems to block forever since I don't return from `peek()` whatever the number of characters that I type (pressing enter every now and then to flush).  
  
```cpp  
#include <iostream>  
#include <boost/iostreams/filtering_stream.hpp>  
  
int main()  
{  
  boost::iostreams::filtering_istream in;  
  in.push(std::cin); // tried in.push(boost::ref(std::cin))  
  
  if ('a' == std::cin.peek()) std::cout << "ok";  
  if ('a' == in.peek()) std::cout << "ok"; // blocks whatever the amount of characters typed, I expected it to behave like the std::cin.  
  
  return 0;  
}  
```  
  
I could not find any indication in the [documentation](https://www.boost.org/doc/libs/1_75_0/libs/iostreams/doc/index.html) that suggests I am not using the library correctly. I tried to wrap `std::cin` with `boost::ref` when pushing it to the filtering stream but it did not change the behavior.  
  
Interestingly, the problem only occurs when interactively typing to the standard input. If I provide data to the standard input through a pipeline, it works as expected:  
```  
echo "aaaa" | program  
```  
  
I tested the snippet with Visual Studio 2017 (boost 1.75.0 and 1.70.0) and gcc9.3 (boost 1.72.0) and none worked.  
  
Thank you in advance for your help in figuring out what goes wrong.

---

## Comment 1

> Username: yobeonline  
> Created at: 2021-01-25 09:58:41 UTC  
> Url: https://github.com/boostorg/iostreams/issues/124#issuecomment-766698065  

For information, I also submitted this question on stackoverflow : https://stackoverflow.com/questions/65861450/why-does-peeking-into-stdcin-through-a-boostiostreamsfiltering-istream-for

---

## Comment 2

> Username: rdoeffinger  
> Created at: 2022-02-01 21:56:52 UTC  
> Url: https://github.com/boostorg/iostreams/issues/124#issuecomment-1027325220  

It doesn't have to do with peek, the same happens with get.  
And the reason is simply the big (4096 bytes I think) buffer that filtering_stream adds.  
I've not had the patience to try, but if you insert those 4096 characters it should also unblock.  
It's not quite the same as it still requires an enter key press, but you can specify the buffer size like:  
in.push(std::cin, 1);  
and it's almost the same.
