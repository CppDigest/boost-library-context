# #1299 - "Wrapper" Concept [Closed]

> Username: vector-of-bool  
> Created at: 2018-11-12 23:15:42 UTC  
> Updated at: 2019-02-16 13:54:56 UTC  
> Closed at: 2019-02-16 13:54:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1299  

Following a discussion on Slack, there seems to be an ethereal concept waiting to be given a name.  
  
Asio's most prominent implementer is probably `ssl::stream<Stream>`, which "wraps" the `Stream`. The important member types are `next_layer_type` and `lowest_layer_type`, and the corresponding methods `next_layer()`, and `lowest_layer()`. Both return a reference to the corresponding wrapped "layer", with both `const` and non-`const`-qualified methods to return `const` and non-`const` references, respectively.  
  
It isn't necessary that the wrapping class implement the same operations as the underlying stream, or of any particular `*Stream` concept. In fact, it may not necessarily be wrapping a `Stream` (For example, a wrapper over UDP sockets).  
  
Some types, like `basic_stream_socket<>` provide a `lowest_layer()`, but no `next_layer`, where `lowest_layer()` simply returns the socket itself. These are _not_ wrappers.  
  
What the concept might look like:  
  
```c++  
template <typename T>  
concept IOObjectWrapper = requires(T w, const T cw){  
    // The type being wrapped  
    typename T::next_layer_type;  
    // The inner-most wrapped type  
    typename T::lowest_layer_type;  
    // Get a reference to the next layer  
    { w.next_layer() } -> typename T::next_layer_type&;  
    // Get a reference to the lowest layer  
    { w.lowest_layer() } -> typename T::lowest_layer_type&;  
    // Get a const-reference to the next layer  
    { cw.next_layer() } -> const typename T::next_layer_type&;  
    // Get a const-reference to the lowest layer  
    { cw.lowest_layer() } -> const typename T::lowest_layer_type&;  
};  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-12 23:59:40 UTC  
> Updated at: 2018-11-13 00:06:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1299#issuecomment-438076418  

bikeshed:  
Concept Name: _Layered_  
Metafunctions: `is_layered<T>`, `is_layered_t<T>`, `lowest_layer<T>`, `lowest_layer_t<T>`  
Nested types: `next_layer_type`, `lowest_layer_type`  
Member functions: `next_layer()`, `lowest_layer()`  
Free functions: `get_lowest_layer(T&)`

---

## Comment 2

> Username: djarek  
> Created at: 2018-11-13 20:22:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1299#issuecomment-438422857  

I'd add: member functions: `get_executor() noexcept`.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2019-01-13 02:45:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1299#issuecomment-453797309  

Yep this is a thing

---

## Comment 4

> Username: vinniefalco  
> Created at: 2019-02-16 13:54:55 UTC  
> Url: https://github.com/boostorg/beast/issues/1299#issuecomment-464349023  

Well, this is done but it didn't turn out how we thought (but it still achieves the goal). What we have is `get_lowest_layer` and a set of type traits. The only soft requirement is that a stream has a `next_layer` function. In the future we can do some kind of `get_layer` thing.
