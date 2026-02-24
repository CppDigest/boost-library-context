# #196 - move span to boost::core:: [Open]

> Username: vinniefalco  
> Created at: 2025-05-25 17:06:54 UTC  
> Updated at: 2025-05-26 23:17:42 UTC  
> Url: https://github.com/boostorg/core/issues/196  

I think `span` should be declared and defined in `boost::core::` so that one may write `core::span` when inside the `boost` namespace. We should still lift it into the enclosing `boost::` namespace, so this is in addition to not in lieu of. This should not break anything.

---

## Comment 1

> Username: glenfe  
> Created at: 2025-05-26 15:49:06 UTC  
> Url: https://github.com/boostorg/core/issues/196#issuecomment-2910150589  

I can understand doing this for new components in Core, but why would we want to do this for existing components?

---

## Comment 2

> Username: Lastique  
> Created at: 2025-05-26 15:54:52 UTC  
> Url: https://github.com/boostorg/core/issues/196#issuecomment-2910161840  

One big reason to place components into more compartmentalized namespaces is avoiding unintended conflicts caused by ADL. When a type is likely to be passed as function or template parameters, or used as a base class, you don't want it to pull lots of unrelated stuff from wide namespaces, such as `boost`.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2025-05-26 19:57:46 UTC  
> Updated at: 2025-05-26 19:58:52 UTC  
> Url: https://github.com/boostorg/core/issues/196#issuecomment-2910549722  

> why would we want to do this for existing components?  
  
I want to reiterate that this proposal does not break anything. The reason we want to do this for existing components is for teaching/documentation. It is better to see `core::span` in header files and documentation rather than `span` or `boost::span` as this makes the origin of the symbol clear and unambiguous.  
  
Existing code deeply nested in the `boost` namespace can still utter `span` if it wants to, with the ambiguities it brings. Or authors can opt-in to less ambiguity by writing `core::span` (my preference) or the more wordy `boost::core::span`.  
  
Lately I have been writing `system::error_code` and `core::string_view` instead of using aliases to those types in my own namespace for clarity, as when you have N libraries each providing aliases for error codes, string views, and other things, the result is unergonomic.

---

## Comment 4

> Username: glenfe  
> Created at: 2025-05-26 23:17:38 UTC  
> Url: https://github.com/boostorg/core/issues/196#issuecomment-2910751246  

That sounds fine; I have no objection to it.
