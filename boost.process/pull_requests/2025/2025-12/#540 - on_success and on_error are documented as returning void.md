# #540 on_success and on_error are documented as returning void [Open]

> Username: krwalker  
> Created at: 2025-12-31 20:19:48 UTC  
> Updated at: 2026-02-11 02:50:29 UTC  
> Url: https://github.com/boostorg/process/pull/540  

As a follow-on to #491, the `probe_on_success` and `probe_on_error` calls use `std::is_same<>` return-type checks. They should be updated to check for `void`, or the documentation should be changed to say they return `error_code`.

---
