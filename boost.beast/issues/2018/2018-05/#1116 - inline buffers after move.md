# #1116 - inline buffers after move [Closed]

> Username: vinniefalco  
> Created at: 2018-05-04 15:36:36 UTC  
> Updated at: 2022-10-04 06:42:09 UTC  
> Closed at: 2022-10-04 06:42:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1116  

Docs for dynamic buffers with inline storage (e.g. `static_buffer`) should state that returned buffer sequences are invalidated after a move.

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-10-04 06:42:09 UTC  
> Url: https://github.com/boostorg/beast/issues/1116#issuecomment-1266470913  

They don't have move constructors, so this should be clear from the table added for #891.
