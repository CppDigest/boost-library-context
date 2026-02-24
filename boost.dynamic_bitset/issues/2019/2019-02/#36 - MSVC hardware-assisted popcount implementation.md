# #36 - MSVC hardware-assisted popcount implementation [Closed]

> Username: jeking3  
> Created at: 2019-02-21 16:41:07 UTC  
> Updated at: 2019-04-26 12:55:54 UTC  
> Closed at: 2019-04-26 12:55:54 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/issues/36  

In #25 support was added for hardware-assisted popcount.  
In #33 it was noted that the implementation for MSVC failed to check for CPU support, and the MSVC documentation states the behavior is undefined in this case.  
In #35 the MSVC implementation was disabled to resolve #33, which leaves the library performance in this respect the same as it was in 1.68.0.  
  
This issue is a placeholder to come back around and clean up the implementation so it can leverage hardware assist safely with MSVC.  There are some thoughts in the comments for #33 regarding alternate implementations that are useful when making a decision here.
