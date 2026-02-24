# #419 - wrapper function taking exe and args [Open]

> Username: user706  
> Created at: 2024-10-30 11:50:47 UTC  
> Updated at: 2024-10-30 12:34:40 UTC  
> Url: https://github.com/boostorg/process/issues/419  

How does one write a wrapper function taking `boost::process::exe` and `boost::process::args`.  
  
is `boost::process::exe` even a type? seems not...  
  
What trickery is this?  
Thanks.

---

## Comment 1

> Username: user706  
> Created at: 2024-10-30 12:10:32 UTC  
> Updated at: 2024-10-30 12:15:01 UTC  
> Url: https://github.com/boostorg/process/issues/419#issuecomment-2446925853  

this does not compile:  
  
  
  
```cpp  
#include <boost/process.hpp>  
#include <string>  
  
std::string getStdOutFromCommand(const std::decay_t<decltype(boost::process::exe)> &exe, const std::decay_t<decltype(boost::process::args)> &args)  
{  
    boost::process::ipstream pipe_stream;  
    boost::process::child c(exe, args, boost::process::std_out > pipe_stream);  
   // ...  
}  
```

---

## Comment 2

> Username: klemens-morgenstern  
> Created at: 2024-10-30 12:34:39 UTC  
> Url: https://github.com/boostorg/process/issues/419#issuecomment-2446988646  

Switch to boost.process v2 and use `filesystem::path` & `vector<string>`. Seriously, this trickery was a mistake.
