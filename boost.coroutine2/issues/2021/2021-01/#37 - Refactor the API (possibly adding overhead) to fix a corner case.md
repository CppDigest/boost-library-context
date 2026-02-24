# #37 - Refactor the API (possibly adding overhead) to fix a corner case [Open]

> Username: jhcarl0814  
> Created at: 2021-01-16 13:17:16 UTC  
> Updated at: 2021-01-17 23:44:05 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/37  

According to the documentation:    
1. push_type does not enter coroutine-function on construction;  
2. the first call of push_type::operator() synthesizes a complementary pull_type and passes it as reference to coroutine-function  
  
But what happens if:    
 - `push_type coro([](pull_type&){ return; });`  
 - the user finds that `bool(coro) == true` (or: `begin(coro) != end(coro)`) so the user would like to perform `coro(value_of_T)` (or: `*begin(coro) = value_of_T`)  
  
It seems the value will be lost.    
  
### The problem is: you don't know whether an output range is empty unless you push a value to it first.    
(Edit: sorry for the mistake, it's the last value that will always be lost if the output range is not infinite.)    
```cpp  
#include<boost/coroutine2/all.hpp>  
#include<iostream>  
#include<iomanip>  
#include<algorithm>  
#include<utility>  
int main()  
{  
    typedef boost::coroutines2::coroutine<std::string> coro_t;  
  
    struct FinalEOL {  
        ~FinalEOL() {  
            std::cout << std::endl;  
        }  
    };  
  
    const int num = 5, width = 15;  
    coro_t::push_type writer(  
        [&](coro_t::pull_type& in) {  
            //// finish the last line when we leave by whatever means  
            //FinalEOL eol;  
            // pull values from upstream, lay them out 'num' to a line  
            //for (;;) {  
            //    for (int i = 0; i < num; ++i) {  
            //        // when we exhaust the input, stop  
            //        if (!in) return;  
            //        std::cout << std::setw(width) << in.get();  
            //        // now that we've handled this item, advance to next  
            //        in();  
            //    }  
            //    // after 'num' items, line break  
            //    std::cout << std::endl;  
            //}  
        });  
  
    std::vector<std::string> words{  
        "peas", "porridge", "hot", "peas",  
        "porridge", "cold", "peas", "porridge",  
        "in", "the", "pot", "nine",  
        "days", "old" };  
  
    //std::copy(begin(words), end(words), begin(writer));  
  
    // safe version of std::copy that takes care of end(dest)  
    auto [words_it, writer_it] = std::pair{ begin(words),begin(writer) };  
    for (; words_it != end(words) && writer_it != end(writer); ++words_it, ++writer_it)  
    {  
        *writer_it = *words_it;  
    }  
  
    // print remaining words; the first word is lost  
    for (; words_it != end(words); ++words_it)  
    {  
        std::cout << *words_it << "\t";  
    }  
}  
```  
  
An applicable approach would be to resume the coroutine in `begin(coro)` like https://github.com/lewissbaker/cppcoro#generatort .    
  
Here is the current API and the imagined API:    
  
current API:    
|                                        | pull_type                 | framework-generated push_type for pull_type | push_type                                                                                           | framework-generated pull_type for push_type |  
|----------------------------------------|---------------------------|---------------------------------------------|-----------------------------------------------------------------------------------------------------|---------------------------------------------|  
| constructor                            | enter coroutine-function  |                                             |                                                                                                     |                                             |  
| operator()<br />iterator::operator++() | resume coroutine-function |                                             |                                                                                                     | suspend coroutine-function                  |  
| get()                                  | transfer data             |                                             |                                                                                                     | transfer data                               |  
| operator()<br />iterator::operator=()  |                           | transfer data, suspend coroutine-function   | (first time: transfer data, enter coroutine-function)<br />transfer data, resume coroutine-function |                                             |  
  
imagined API:    
|                                       | pull_type                     | framework-generated push_type for pull_type | push_type                     | framework-generated pull_type for push_type |  
|---------------------------------------|-------------------------------|---------------------------------------------|-------------------------------|---------------------------------------------|  
| constructor                           |                               |                                             |                               |                                             |  
| start()<br />begin(coro)              | ①enter coroutine-function    | ②                                          | ⒈enter coroutine-function    | ⒉suspend coroutine-function                |  
| advance()<br />iterator::operator++() | ⑥⑩resume coroutine-function | ④⑧suspend coroutine-function              | ⒋⒏resume coroutine-function | ⒍⒑suspend coroutine-function              |  
| get()                                 | ⑤⑨transfer data             |                                             |                               | ⒌⒐transfer data                           |  
| set()<br />iterator::operator=()      |                               | ③⑦transfer data                           | ⒊⒎transfer data             |                                             |  
  
Note that in the column of `push_type`: if after `⒊transfer data` instead of `⒋resume coroutine-function` you `⒋abandon the coroutine`, then the `value_of_T` will still be lost. But this time it is the user of API to blame, not the framework.    
  
These are what you can do:    
| utility                                                                                                                                                     | current API                                                          | imagined API                                                                                                                                                                                |  
|-------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|  
| merge pipe and source into source<br />`void(pull_type<T1>,push_type<T2>) + pull_type<T1> -> pull_type<T2>`                                                 | trivial for loop                                                     | trivial for loop                                                                                                                                                                            |  
| merge pipe and source into source<br />`T2(T1) + pull_type<T1> -> pull_type<T2>`                                                                            | trivial for loop                                                     | trivial for loop                                                                                                                                                                            |  
| merge pipe and sink into sink<br />`void(pull_type<T1>,push_type<T2>) + push_type<T2> -> push_type<T1>`                                                     | trivial for loop                                                     | trivial for loop                                                                                                                                                                            |  
| merge pipe and sink into sink<br />`T2(T1) + push_type<T2> -> push_type<T1>`                                                                                | trivial for loop                                                     | trivial for loop                                                                                                                                                                            |  
| merge source and sink and stops when either one exhausts<br />`std::pair<source_it_t, sink_it_t> safe_copy(source_begin, source_end, sink_begin, sink_end)` | **the first value of source will be lost if sink is an empty range** | trivial for loop<br />the user will not lose the first value of source because if sink is an empty range then we have `begin(sink) == end(sink)` after construction and start()/begin(sink) |
