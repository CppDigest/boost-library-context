# #101 - Support raw number literals in basic_parser [Closed]

> Username: rtobar  
> Created at: 2020-05-28 07:27:37 UTC  
> Updated at: 2020-08-12 02:11:13 UTC  
> Closed at: 2020-08-12 02:11:13 UTC  
> Url: https://github.com/boostorg/json/issues/101  

The current set of virtual functions that subclasses of `basic_parser` need to implement provides users with entry points for receiving `int64`, `uint64` and `double` numerical values out of the parsing process.  
  
JSON content however might contain numerical values outside of these types' ranges, or that cannot be exactly represented by any of them (e.g., `"1e400"`, `"1e-400"` or `"3.141592653589793238462643383279"`). In these cases users loose have no access to the original values as seen in the JSON stream, and can only access the values as calculated by `basic_parser`, which will already be overflown, underflown, or otherwise misrepresented, therefore loosing information (e.g., `Infinity`, `0` and `3.141592653589793115997963468544185161590576171875` for the examples above). Adding a way for users to get access to the original string values for those numbers would allow them to access the original values without information loss.  
  
Just for reference, I only have experience with yajl, which gives user the option to provide callbacks either for integer/double values, or for numbers-as-string. The two modes are mutually exclusive, so when using the latter users have to do their own number parsing. I have no idea how this support would be best implemented in this library though, which otherwise has given me very good results so far.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-05-30 03:25:58 UTC  
> Url: https://github.com/boostorg/json/issues/101#issuecomment-636268612  

This can be done by adding a handler member `on_number_part` for partial strings, and then adding a `string_view` to the existing on_number handlers. The compiler should in theory optimize it away if it is not used, since the handler's definition is visible to the parsing function.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-08-10 00:09:08 UTC  
> Url: https://github.com/boostorg/json/issues/101#issuecomment-671118319  

@sdkrystian I believe this was merged?

---

## Comment 3

> Username: sdkrystian  
> Created at: 2020-08-10 01:42:41 UTC  
> Url: https://github.com/boostorg/json/issues/101#issuecomment-671131574  

@vinniefalco Oh, oops. I have the branch, but it never got merged. I'll rebase it and open a PR.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-08-12 02:11:13 UTC  
> Url: https://github.com/boostorg/json/issues/101#issuecomment-672464166  

This is merged
