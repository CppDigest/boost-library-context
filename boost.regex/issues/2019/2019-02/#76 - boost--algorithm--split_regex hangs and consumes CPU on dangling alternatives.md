# #76 - boost::algorithm::split_regex hangs and consumes CPU on dangling alternatives [Closed]

> Username: directionless  
> Created at: 2019-02-16 06:50:11 UTC  
> Updated at: 2020-01-23 03:04:22 UTC  
> Closed at: 2020-01-19 18:09:38 UTC  
> Url: https://github.com/boostorg/regex/issues/76  

I happened to typo a regex, and I noticed that `boost::algorithm::split_regex` started consuming a bunch of CPU. I don't know is this would have eventually caught itself and terminating. The regex in question was `;|`, and this appears to be true for any dangling `|`.   
  
I'm a bit surprised that `boost::regex` allowed `;|` as valid, given split_regex's behavior.   
  
Simple example up at https://gist.github.com/directionless/c38eb7dc216bcd93b40c3ecaf99f326e  
  
I tested this on darwin with boost 1.67, and on linux with boost 1.65

---

## Comment 1

> Username: jzmaddock  
> Created at: 2020-01-19 18:09:38 UTC  
> Url: https://github.com/boostorg/regex/issues/76#issuecomment-576030237  

Sorry to split hairs, but can you please file this bug against Boost.Algorithm?  
  
There are a number of regular expressions which can successfully match zero characters - for example "^", and I've confirmed that these all break Boost.Algorithm.  
  
With regard to ";|" being allowed: it wasn't historically, but was changed for Perl compatibility.  It does have a small number of legitimate uses, perhaps a more obvious example would be ";|^" which treats ";" plus line-starts as separators, and again can match zero characters.

---

## Comment 2

> Username: directionless  
> Created at: 2020-01-23 03:04:22 UTC  
> Url: https://github.com/boostorg/regex/issues/76#issuecomment-577483982  

Filed as https://github.com/boostorg/algorithm/issues/68  
  
At this point, my buzzers are _also_ finding this bug.
