# #191 - Stream corruption when reading from forward-iterator range exceeding stream buffer size [Open]

> Username: michaeldonovan  
> Created at: 2026-02-20 17:43:34 UTC  
> Updated at: 2026-02-20 17:43:34 UTC  
> Url: https://github.com/boostorg/iostreams/issues/191  

It would appear that when an istream reads from a forward iterator range (e.g. `boost::adaptors::transformed`) larger than the stream's internal buffer, a garbage byte gets inserted at the stream's buffer boundary.  
  
## Reproducer  
  
```cpp  
#include <iostream>  
#include <iomanip>  
#include <string>  
#include <boost/iostreams/filtering_stream.hpp>  
#include <boost/range/adaptor/transformed.hpp>  
#include <boost/range/iterator_range.hpp>  
  
int main() {  
    std::vector<int> input(5000, 0x41);  
    auto transformed = input | boost::adaptors::transformed([](int c) { return static_cast<char>(c); });  
  
    boost::iostreams::filtering_istream in(boost::make_iterator_range(transformed));  
    std::string output(std::istreambuf_iterator<char>(in), {});  
  
    std::cout << "Input size: " << input.size() << ", output size: " << output.size() << "\n";  
    for (size_t i = 0; i < std::max(input.size(), output.size()); ++i) {  
        if (input[i] != output[i]) {  
            std::cout << "First mismatch at [" << i << "]";  
            std::cout << " expected 0x"  
                        << std::hex << std::setw(2) << std::setfill('0') << input[i]  
                        << ", got 0x"  
                        << std::hex << std::setw(2) << std::setfill('0') << static_cast<int>(output[i]);  
            break;  
        }  
    }  
}  
```  
### Output:  
```  
Input size: 5000, output size: 5001  
First mismatch at [4096] expected 0x41, got 0x00  
```                                                                                                     
  
## Cause  
  
[`range_adapter_impl<std::forward_iterator_tag>::read`](https://github.com/boostorg/iostreams/blob/dacdb3db57e9f372ad575f02a45b5efb439d8889/include/boost/iostreams/detail/adapter/range_adapter.hpp#L105C1-L112C6)  post-decrements `rem` in the loop condition:  
  
```cpp  
std::streamsize rem = n;  
while (cur != last && rem-- > 0) *s++ = *cur++;  
return n - rem != 0 ? n - rem : -1;  
```  
  
When exactly `n` chars have been read but `cur != last` , the loop evaluates `cur != last` → true, then `rem-- > 0` → `0 > 0` is false so the loop exits, but `rem` is now `-1`. The function returns `n + 1`, overrunning the buffer.  
  
`range_adapter_impl<std::forward_iterator_tag>::write` appears to have the same issue.
