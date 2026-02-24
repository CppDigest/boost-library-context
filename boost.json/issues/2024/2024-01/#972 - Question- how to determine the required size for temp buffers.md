# #972 - Question: how to determine the required size for temp buffers? [Closed]

> Username: niXman  
> Created at: 2024-01-12 15:13:05 UTC  
> Updated at: 2024-01-21 13:38:14 UTC  
> Closed at: 2024-01-21 13:38:14 UTC  
> Url: https://github.com/boostorg/json/issues/972  

hello guys,  
  
just reading [that part](https://www.boost.org/doc/libs/1_84_0/libs/json/doc/html/json/input_output.html#json.input_output.parsing.avoiding_dynamic_allocations) of the docs I wondered what determines the sizes of the auto-arrays given in the code?  
```cpp  
/*  Parse JSON and invoke the handler  
  
    This function parses the JSON specified in `s`  
    and invokes the handler, whose signature must  
    be equivalent to:  
  
        void( value const& jv );  
  
    The operation is guaranteed not to perform any  
    dynamic memory allocations. However, some  
    implementation-defined upper limits on the size  
    of the input JSON and the size of the resulting  
    value are imposed.  
  
    Upon error, an exception is thrown.  
*/  
template< class Handler >  
void do_rpc( string_view s, Handler&& handler )  
{  
    unsigned char temp[ 4096 ];                 // The parser will use this storage for its temporary needs  
    parser p(                                   // Construct a strict parser using the temp buffer and no dynamic memory  
        get_null_resource(),                    // The null resource never dynamically allocates memory  
        parse_options(),                        // Default constructed parse options allow only standard JSON  
        temp );  
  
    unsigned char buf[ 16384 ];                 // Now we need a buffer to hold the actual JSON values  
    static_resource mr2( buf );                 // The static resource is monotonic, using only a caller-provided buffer  
    p.reset( &mr2 );                            // Use the static resource for producing the value  
    p.write( s );                               // Parse the entire string we received from the network client  
  
    // Retrieve the value and invoke the handler with it.  
    // The value will use `buf` for storage. The handler  
    // must not take ownership, since monotonic resources  
    // are inefficient with mutation.  
    handler( p.release() );  
}  
  
```  
- do they depend on something?  
- do they depend on the JSON size?  
- is there any formula or helper functions?  
  
[there](https://www.boost.org/doc/libs/1_84_0/libs/json/doc/html/json/ref/boost__json__stream_parser.html#json.ref.boost__json__stream_parser.temporary_storage) I found a few words on the topic, but again nothing about the logic for choosing the sizes of a temporary buffer...  
  
  
thanks!

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-01-12 15:24:20 UTC  
> Url: https://github.com/boostorg/json/issues/972#issuecomment-1889503927  

These are just reasonable upper limits. You have to have explicit upper limits if you want to avoid dynamic allocations entirely. And if you allow dynamic allocations, you just have your available system memory as the implicit upper limit.

---

## Comment 2

> Username: niXman  
> Created at: 2024-01-12 15:27:03 UTC  
> Url: https://github.com/boostorg/json/issues/972#issuecomment-1889508189  

thank you, but I already knew that.  
my questions are different.

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-01-12 16:20:56 UTC  
> Url: https://github.com/boostorg/json/issues/972#issuecomment-1889592950  

So, the second array is the storage for the actual resulting `json::value` and all of its children. So, yes it should be large enough for the data you may receive.  
  
The first array is _temporary_ storage. Its size also depends on the size of the input, but not as directly. In the worst case you might require as much storage as you need for the result, but e.g. strings are directly allocated inside the intended storage if possible.  
  
So, we do not have a specific formula, but it should be C * N, where N is the size of the input in bytes, for both arrays, but C is larger for the second array.

---

## Comment 4

> Username: niXman  
> Created at: 2024-01-16 03:53:55 UTC  
> Url: https://github.com/boostorg/json/issues/972#issuecomment-1893032024  

thank you for the explanation!  
  
im sorry, but It seems to me, or is this your guess?

---

## Comment 5

> Username: niXman  
> Created at: 2024-01-16 03:57:48 UTC  
> Url: https://github.com/boostorg/json/issues/972#issuecomment-1893034206  

> the second array is the storage for the actual resulting json::value and all of its children.  
  
then I should ask one more question: what is the formula to calculate the required mem size to store, for example for `[0,1,2,3]`, and for `["0","1","2","3"]` ?  
  
:)

---

## Comment 6

> Username: grisumbras  
> Created at: 2024-01-17 22:37:09 UTC  
> Url: https://github.com/boostorg/json/issues/972#issuecomment-1897198903  

It's not my guess, I checked with the code just in case.  
  
Due to Small Buffer Optimisation, in both cases the second array needs to be at least `4 * sizeof(value)`. If the strings were big, you'd also need storage for them.

---

## Comment 7

> Username: niXman  
> Created at: 2024-01-21 13:38:14 UTC  
> Url: https://github.com/boostorg/json/issues/972#issuecomment-1902632424  

got it, thanks!  
  
closed.
