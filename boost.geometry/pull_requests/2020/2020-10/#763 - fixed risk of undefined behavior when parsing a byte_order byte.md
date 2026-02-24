# #763 fixed risk of undefined behavior when parsing a byte_order byte [Merged]

> Username: CG-SD  
> Created at: 2020-10-09 10:46:57 UTC  
> Updated at: 2020-11-23 08:23:26 UTC  
> Merged at: 2020-11-21 01:53:40 UTC  
> Closed at: 2020-11-21 01:53:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/763  

tried to parse a spatialite WKB blob with this code but the byte_order byte was replaced by a '69' token :  
 parsing this byte is successful but if the value read is not part of the enum, the function returns true but the order parameter is left untouched.   
Calls to that function elsewhere in the code do not initialise the order parameter.  
see https://github.com/boostorg/geometry/blob/869e72fc01f2fce5ec3bcdfb91afab65ebd3ec6f/include/boost/geometry/extensions/multi/gis/io/wkb/detail/parser.hpp#L83  
  
another option would be to return false in that case, but it didn't fit my need

---

## Comment 1

> Username: awulkiew  
> Created_at: 2020-11-21 01:53:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/763#issuecomment-731488535  

Thanks!

---
