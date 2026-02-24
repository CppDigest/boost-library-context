# #361 - Document capture list for BOOST_COMPUTE_CLOSURE() [Open]

> Username: kylelutz  
> Created at: 2014-12-29 18:08:08 UTC  
> Updated at: 2017-03-21 04:14:47 UTC  
> Url: https://github.com/boostorg/compute/issues/361  

Document the capture list for `BOOST_COMPUTE_CLOSURE()` better. Indicate that it is a Boost.PP tuple rather than a sequence (perhaps have an example showing capturing multiple values).  
  
From the review:  
  
```  
Also, <http://kylelutz.github.io/compute/BOOST_COMPUTE_CLOSURE.html>  
doesn't explain what format the lists (arguments and capture) should be  
in.  Preprocessor functions often take lists in other formats, such as a  
Boost.PP Sequence  
<http://www.boost.org/doc/libs/1_57_0/libs/preprocessor/doc/data/sequences.html>,  
and your example only has one-element lists, so it's not enough to  
clarify this point.  (To be clear: I'm not suggesting you should use  
Boost.PP sequences, merely that you should make it clear in the docs  
whether you are).  
```  
##
