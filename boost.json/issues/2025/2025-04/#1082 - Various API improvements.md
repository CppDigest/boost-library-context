# #1082 - Various API improvements [Open]

> Username: grisumbras  
> Created at: 2025-04-22 12:08:50 UTC  
> Updated at: 2025-04-25 12:59:16 UTC  
> Url: https://github.com/boostorg/json/issues/1082  

* [ ] `array::resize(std::size_t, value const&)` --- replace the last parameter with `value_ref` to avoid constructing the value if not necessary.  
* [ ] Review all "basic guarantee" functions; potentially strengthen their guarantee, alternatively clarify which arguments can end up changed when an error occurs.
