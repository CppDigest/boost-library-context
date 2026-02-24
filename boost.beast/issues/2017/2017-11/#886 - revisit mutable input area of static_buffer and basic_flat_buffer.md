# #886 - revisit mutable input area of static_buffer and basic_flat_buffer [Closed]

> Username: vinniefalco  
> Created at: 2017-11-13 19:47:25 UTC  
> Updated at: 2018-02-27 21:08:53 UTC  
> Closed at: 2018-02-27 21:08:53 UTC  
> Url: https://github.com/boostorg/beast/issues/886  

These input areas are mutable to optimize the websocket implementation. Perhaps websocket can use a `buffers_adapter` over the dynamic buffer output area (return value of `prepare) instead, and keep the composed operation trivial. We can investigate a specialization of `buffers_adapter` for `boost::asio::mutable_buffer` for optimization.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-11-14 00:23:33 UTC  
> Url: https://github.com/boostorg/beast/issues/886#issuecomment-344104260  

Thinking about it further, websocket can't use `buffers_adapter`. It has to modify the input area, because the data is already committed (in the implementation of `read`). The safest way to resolve this is to give websocket a private implementation of `static_buffer` with a mutable input area, and revert the public dynamic buffer implementations to making their input areas constant buffer sequences.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-02-27 21:08:51 UTC  
> Url: https://github.com/boostorg/beast/issues/886#issuecomment-369026660  

This is done, `data` no longer returns a mutable buffer sequence. And `static_buffer` provides the `mutable_data` member which allows modification.
