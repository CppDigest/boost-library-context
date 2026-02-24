# #653 - Bug in segments compare [Closed]

> Username: alandefreitas  
> Created at: 2023-01-10 19:59:33 UTC  
> Updated at: 2023-01-19 01:24:14 UTC  
> Closed at: 2023-01-19 01:24:14 UTC  
> Url: https://github.com/boostorg/url/issues/653  

The segments compare function has a bug when consuming the last character from the first segment.   
  
The function to consume the last character should check if the first segment is already consumed before outputting the "../" prefix but it was going straight to that strategy when the current segment is the first segment.  
  
The reason we didn't catch this bug in our current tests is that the tests used short segments, which made the comparison equivalent to comparing ".." for the first segments in the tests we have.
