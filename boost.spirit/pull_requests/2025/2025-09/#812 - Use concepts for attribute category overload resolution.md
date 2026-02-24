# #812 Use concepts for attribute category overload resolution [Merged]

> Username: saki7  
> Created at: 2025-09-06 16:56:44 UTC  
> Updated at: 2025-09-06 20:40:59 UTC  
> Merged at: 2025-09-06 19:55:57 UTC  
> Closed at: 2025-09-06 19:55:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/812  

Part of boostorg/spirit_x4#4  
  
`x3::traits::move_to`: Dump tag dispatching and use concepts for overload resolution. This drastically improves compilation speed and prints significantly concise errors on ill-formed programs, thanks to the reduced nesting level on the entire control flow of X3. Also partially (but not yet completely) improves boostorg/spirit_x4#5 due to reduced MPL usage.  
  
`x3::detail::parse_into_container`: Ditto.  
  
`support/traits/optional_traits.hpp`:  
  `BOOST_SPIRIT_X3_USE_BOOST_OPTIONAL`: new macro.  
  Default to `boost::optional` unless above macro is defined as `0`. Implements  
  `std::optional` handling regardless of the value; fixes boostorg/spirit_x4#15.  
  Note that most test suites are intentionally not defining above macro as `0`   
  (yet) to make sure the change does not break existing codes.  
  
`x3::optional`: Ideally this should've split into a separate PR, but the new attribute category handling requires the overhaul on this.  
  
`core/proxy.hpp`: Removed. `x3::optional` was the only derived parser which used this feature for years. We shouldn't support any overly generic 'core' features whose extandable use case is unknown even to the core components.  
  
`extract_int`, etc.: Adjusted to properly "move" the local attribute variable before passing it to `x3::traits::move_to`.  
  
`char_parser`: Ditto, but this constructs temporary value instead of applying `std::as_const`. Const references are costly for primitive type like `Char` thus prvalue should be constructed here.  
  
tests: No semantic changes intended. All changes exists solely for adapting to the new attribute category handling (implementation details.)

---

## Comment 1

> Username: saki7  
> Created_at: 2025-09-06 19:56:55 UTC  
> Url: https://github.com/boostorg/spirit/pull/812#issuecomment-3263170940  

Self-merged; these are implementation details and does not change the semantics of public API.

---
