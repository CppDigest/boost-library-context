# #462 - Bug in `environment::key_char_traits::move` on Windows [Closed]

> Username: js-nano  
> Created at: 2025-01-29 16:00:04 UTC  
> Updated at: 2025-03-06 09:43:44 UTC  
> Closed at: 2025-03-06 09:43:44 UTC  
> Url: https://github.com/boostorg/process/issues/462  

There's a bug in `environment::key_char_traits::move()` on Windows:  
  
https://github.com/boostorg/process/blob/bb375f50bd5b362b087aab326df158a440882e29/include/boost/process/v2/detail/environment_win.hpp#L88  
  
The final parameter should point one-past-the-end of the range to be moved to -- https://en.cppreference.com/w/cpp/algorithm/move_backward  
  
The correct code is:  
```c++  
    return std::move_backward(s2, s2 + n, s1 + n);  
```  
  
# Analysis  
  
I saw segfaults when adding elements to a `std::unordered_map<env::key, env::value>` from an `env::key_view`/`env::value_view`  
  
While investigating, I noticed that, occasionally, a newly constructed key would be empty, despite all the parameters looking plausible.  I eventually narrowed it down to the use of `move_backward` when moving the string contents -- the final parameter should point to the _end_ of the range to be moved to.  
  
A minimal demonstration of the underlying problem:  
```c++  
#include <boost/process/v2/environment.hpp>  
#include <array>  
#include <cassert>  
  
int main() {  
    std::array<boost::process::v2::environment::key::string_type, 2> arr;  
    arr[0] = L"def";  
    arr[1].assign(arr[0].data(), arr[0].size());  
    assert(arr[1] == L"def");  
}  
```  
  
In this demonstration:  
- The source string at `arr[0]` has an address before the destination string `arr[1]`  
- The small string optimization is in effect, so the addresses of the buffers are in the same order as the strings themselves  
- Therefore, in `char_traits::move()`, `s1` (dest) is after `s2` (src)  
- So the `move_backward()` branch is taken  
- `move_backward()` moves the chars before the start of the destination string, due to the 3rd parameter  
- Either you get a segfault (due to writing before the start of the string memory), or the assert fails (due to the contents being written _before_ the target string buffer)

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-02-03 03:01:42 UTC  
> Url: https://github.com/boostorg/process/issues/462#issuecomment-2629789317  

Do you want to create a PR?

---

## Comment 2

> Username: js-nano  
> Created at: 2025-02-03 09:37:34 UTC  
> Url: https://github.com/boostorg/process/issues/462#issuecomment-2630425219  

Very happy to if that makes things easier for you; equally happy for you to make the change if that works for you

---

## Comment 3

> Username: js-nano  
> Created at: 2025-03-06 08:11:20 UTC  
> Url: https://github.com/boostorg/process/issues/462#issuecomment-2703111999  

PR is here:  
https://github.com/boostorg/process/pull/467
