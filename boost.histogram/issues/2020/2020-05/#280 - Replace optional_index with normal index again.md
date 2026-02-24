# #280 - Replace optional_index with normal index again [Open]

> Username: HDembinski  
> Created at: 2020-05-04 19:11:43 UTC  
> Updated at: 2020-05-04 19:11:43 UTC  
> Url: https://github.com/boostorg/histogram/issues/280  

Instead of maintaining a special index state for "out-of-range" during linearization, just add the size of the whole histogram whenever any axis index is out of range. This assures that the final index is past the end, which can be easily detected. This should further reduce the instructions.
