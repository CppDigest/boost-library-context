# #43 - Question: How to correctly rethrow/propagate? [Closed]

> Username: tchatow  
> Created at: 2022-03-16 01:31:12 UTC  
> Updated at: 2022-03-28 20:43:24 UTC  
> Closed at: 2022-03-26 22:00:16 UTC  
> Url: https://github.com/boostorg/leaf/issues/43  

I have a situation that looks something like this, which I am attempting to translate into LEAF:  
  
    try {  
      MaybeThrowingOperation();  
    } catch (const my_error_type&) {  
      if (/* conditional */) {  
        throw;  
      }  
    }  
  
The conditional is not part of the exception type, but comes from the surrounding block, so it seems I can't use a matcher. I also considered simply returning `leaf::new_error(e)`, but if some `on_error` data was attached, this would be discarded. I found `leaf::current_error()` which seems to do the trick. Is using that correct or is there a better way to "rethrow"?  
  
    boost::leaf::try_handle_some(  
      [&] -> boost::leaf::result<void> {  
        // Some operation that returns either a valid result<void>{} or a result with an error_id.  
      },  
      [&](const my_error_type&) -> boost::leaf::result<void> {  
        if (/* conditional */) {  
          return boost::leaf::current_error();  
        } else {  
          return {};  
        }  
      });

---

## Comment 1

> Username: zajo  
> Created at: 2022-03-19 06:27:31 UTC  
> Url: https://github.com/boostorg/leaf/issues/43#issuecomment-1072952763  

Thanks for this, the tutorial is missing this important information, I'll add it tomorrow.  
  
You do it like so:  
  
```  
boost::leaf::try_handle_some(  
  [&] -> boost::leaf::result<void> {  
    // Some operation that returns either a valid result<void>{} or a result with an error_id.  
  },  
  [&](leaf::error_info const & e, const my_error_type&) -> boost::leaf::result<void> {  
    if (/* conditional */) {  
      return e.error();  
    } else {  
      return {};  
    }  
  });  
```  
Any error handler can take `leaf::error_info const &`, it is always available.  
  
As you've discovered, `leaf::current_error` does the trick also, but it may not be correct if you have multiple errors "in flight".

---

## Comment 2

> Username: zajo  
> Created at: 2022-03-19 22:00:00 UTC  
> Url: https://github.com/boostorg/leaf/issues/43#issuecomment-1073118370  

I updated the [error handling section in the documentation](https://boostorg.github.io/leaf/#tutorial-error_handling).

---

## Comment 3

> Username: tchatow  
> Created at: 2022-03-28 20:43:24 UTC  
> Url: https://github.com/boostorg/leaf/issues/43#issuecomment-1081122505  

Thanks, this works perfectly.
