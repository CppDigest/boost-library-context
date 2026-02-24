# #227 - Out of bounds access with specially crafted regular expression [Closed]

> Username: alexsharoff  
> Created at: 2024-10-03 18:42:08 UTC  
> Updated at: 2024-11-06 09:20:08 UTC  
> Closed at: 2024-11-06 09:20:08 UTC  
> Url: https://github.com/boostorg/regex/issues/227  

Running the following program under debug gives an error `xstring(1902) : Assertion failed: cannot dereference string iterator because it is out of range (e.g. an end iterator)`:  
  
```cpp  
#include <boost/regex.hpp>  
#include <string>  
  
int main() {  
   boost::regex rx("(*ACCEPT)*+\\1((*ACCEPT)*+\\K)");  
   std::string str = "Z";  
   boost::smatch what;  
   boost::regex_search(str, what, rx, boost::match_default|boost::match_partial);  
}  
```  
  
Tested on Windows, compiler: Visual Studio 2019.  
  
The above regular expression was found via fuzzing.

---

## Comment 1

> Username: alexsharoff  
> Created at: 2024-10-04 15:12:12 UTC  
> Updated at: 2024-10-04 15:12:32 UTC  
> Url: https://github.com/boostorg/regex/issues/227#issuecomment-2393932538  

If found that after defining `BOOST_REGEX_CXX03` `boost::regex_search` starts throwing an exception:  
`Invalid back reference: specified capturing group does not exist`.

---

## Comment 2

> Username: cmazakas  
> Created at: 2024-10-31 18:12:07 UTC  
> Updated at: 2024-10-31 18:22:14 UTC  
> Url: https://github.com/boostorg/regex/issues/227#issuecomment-2450531251  

@jzmaddock I've made some progress on debugging this issue. It seems like it happens here: https://github.com/boostorg/regex/blob/develop/include/boost/regex/v5/match_results.hpp#L484  
  
`set_second()` sets `.second` for the sub-match but the problem is, it doesn't readjust `.first` so what happens is, in the string `"Z\0"`, we wind up having the submatch start at `\0` and the `second` is at `"Z"`.  
  
I'm not sure what the correct step here is to rectify this issue. It seems like the `if((pos == 2) && !escape_k)` is the one we want to take but I'm not sure if changing this here is correct or not.  
  
Edit:  
  
Forgot add the callstack here, ha ha. This happens in `match_endmark()` as a result of `skip_until_paren()` as a result of `match_accept()`, which makes sense as our regex is created to use the `(*ACCEPT)` Perl extension.  
  
Not sure why the named capture and the named backref make it break here, but it seems to find a case when re-adjusting the `second` itetator where we sort of trip over ourselves, so to speak.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2024-10-31 18:23:09 UTC  
> Url: https://github.com/boostorg/regex/issues/227#issuecomment-2450552421  

Our messages just crossed - see your email ;)

---

## Comment 4

> Username: jzmaddock  
> Created at: 2024-10-31 18:25:51 UTC  
> Url: https://github.com/boostorg/regex/issues/227#issuecomment-2450557746  

Also forgot to say: this is a "should never happen" situation which should be asserted, but that's hard for bidirectional iterators as we can't assert i < j for these, or indeed order them generally.
