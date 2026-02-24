# #164 - Add to_xxx functions equivalents for to<XXX> functions [Closed]

> Username: ldionne  
> Created at: 2015-07-28 18:21:13 UTC  
> Updated at: 2015-11-17 04:56:06 UTC  
> Closed at: 2015-11-17 04:56:06 UTC  
> Url: https://github.com/boostorg/hana/issues/164  

For the same reason as we provide `make_xxx` equivalents for `make<XXX>`.  
Specifically, we should add (at least) the following:  
- [x] `to_tuple` == `to<tuple_tag>`  
- [x] `to_set == to<set_tag>`  
- [x] `to_map == to<map_tag>`  
- [ ] ~~`to_type == to<type_tag>` (although I'm not sure there should be such a thing in the first place?)~~ There's no such thing as a `to<type_tag>`!  
  
To add them, we should do something similar to what's being done for the `make_xxx` functions. For example, for `hana::tuple`, we would document a `to_tuple` function in `boost/hana/fwd/tuple.hpp`, by saying it forwards to `to<tuple_tag>`. This will require including `boost/hana/fwd/core/convert.hpp`. Since the implementation for `hana::tuple` is handled automatically by the `Sequence`, concept, that would be it.  
  
For e.g. `hana::set`, we would need to document `to<set_tag>` (which is not documented, oops!) in the `boost/hana/fwd/set.hpp` header. Then, we would add a `to_set` that forwards to `to<set_tag>` in the `fwd` header too.

---

## Comment 1

> Username: ldionne  
> Created at: 2015-11-14 15:08:03 UTC  
> Url: https://github.com/boostorg/hana/issues/164#issuecomment-156709661  

> For e.g. hana::set, we would need to document to<set_tag> (which is not documented, oops!)  
  
Actually, it _is_ documented in the general section on `hana::set`.
