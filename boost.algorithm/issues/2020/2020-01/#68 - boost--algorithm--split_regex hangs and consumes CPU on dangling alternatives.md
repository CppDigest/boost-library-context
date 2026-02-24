# #68 - boost::algorithm::split_regex hangs and consumes CPU on dangling alternatives [Open]

> Username: directionless  
> Created at: 2020-01-23 03:03:10 UTC  
> Updated at: 2020-01-23 03:03:10 UTC  
> Url: https://github.com/boostorg/algorithm/issues/68  

I happened to typo a regex, and I noticed that `boost::algorithm::split_regex` started consuming a bunch of CPU. I don't know is this would have eventually caught itself and terminating. The regex in question was `;|`, and this appears to be true for any dangling `|`.   
  
I'm a bit surprised that `boost::regex` allowed `;|` as valid, given split_regex's behavior.   
  
Simple example up at https://gist.github.com/directionless/c38eb7dc216bcd93b40c3ecaf99f326e  
  
I tested this on darwin with boost 1.67, and on linux with boost 1.65  
  
(This was moved here from https://github.com/boostorg/regex/issues/76)
