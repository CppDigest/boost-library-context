# #1341 - Exception guarantees [Closed]

> Username: vinniefalco  
> Created at: 2018-12-03 04:01:57 UTC  
> Updated at: 2024-02-14 09:42:05 UTC  
> Closed at: 2024-02-14 09:42:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1341  

Javadocs for dynamic buffers and other appropriate types should have prescribed exception guarantees.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-08 14:41:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1341#issuecomment-445464177  

`flat_buffer` and `multi_buffer` have exception specifications now

---

## Comment 2

> Username: ashtum  
> Created at: 2024-02-14 09:42:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1341#issuecomment-1943398339  

Addressed in https://github.com/boostorg/beast/commit/11c71d118fe297af31efd4a3477c20b6b41cbb50, https://github.com/boostorg/beast/commit/8262405064c7874b48aa934a41b7dfe2e287732b and https://github.com/boostorg/beast/commit/29cf6ce61f39fcee5f86531e2d37562c2d465104.
