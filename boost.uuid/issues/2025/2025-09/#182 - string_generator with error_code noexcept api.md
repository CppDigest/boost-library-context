# #182 - string_generator with error_code noexcept api [Closed]

> Username: slnj  
> Created at: 2025-09-22 02:58:14 UTC  
> Updated at: 2025-12-24 18:27:47 UTC  
> Closed at: 2025-12-24 18:27:47 UTC  
> Url: https://github.com/boostorg/uuid/issues/182  

Would you consider to incorporate to string_generator a new API with error_code as parameter like:  
  
string_generator::operator()( std::basic_string<Ch, Traits, Alloc> const& s, boost::system::error_code&) const noexcept  
  
so that the conversion can be attempted without the performance penalty associated with using exceptions?  
  
Thanks

---

## Comment 1

> Username: pdimov  
> Created at: 2025-09-22 10:19:08 UTC  
> Url: https://github.com/boostorg/uuid/issues/182#issuecomment-3318062977  

This is a good idea in principle. I wonder however whether using `error_code` here wouldn't be overkill because doing it right would require creating a dedicated error category. It might be easier to define an enum with the appropriate error codes and return that instead of `error_code`.

---

## Comment 2

> Username: Lastique  
> Created at: 2025-12-16 16:48:29 UTC  
> Url: https://github.com/boostorg/uuid/issues/182#issuecomment-3661476917  

I always found `string_generator` awkward. I think, a `from_chars` overload would be better. And it would be possible to make it more strict as well, i.e. not allow braces and always require dashes. Then we could add a SIMD version of it. :)  
  
We could also provide a throwing `uuid from_string(string_view)` wrapper on top of it as well to facilitate one-line parsing, like it is possible with `string_generator`. But that'd be just a convenience tool.

---

## Comment 3

> Username: pdimov  
> Created at: 2025-12-22 18:10:30 UTC  
> Url: https://github.com/boostorg/uuid/issues/182#issuecomment-3683287766  

`from_chars` is a very good idea, thanks. I'll be adding it shortly.

---

## Comment 4

> Username: pdimov  
> Created at: 2025-12-23 17:52:58 UTC  
> Url: https://github.com/boostorg/uuid/issues/182#issuecomment-3687483142  

`from_chars` and a `string_generator::operator()` `noexcept` overload added to develop.
