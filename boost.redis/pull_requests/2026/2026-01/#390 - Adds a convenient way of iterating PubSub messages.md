# #390 Adds a convenient way of iterating PubSub messages [Merged]

> Username: anarthal  
> Created at: 2026-01-27 18:54:17 UTC  
> Updated at: 2026-02-23 14:59:45 UTC  
> Merged at: 2026-02-23 14:59:41 UTC  
> Closed at: 2026-02-23 14:59:41 UTC  
> Url: https://github.com/boostorg/redis/pull/390  

Adds push_view and push_parser. This is an input view range that allows parsing PubSub messages from a node tree.  
Adds a doc page on how to use server pushes.  
Adds reference docs to set_receive_response.  
  
close #349

---

## Review 1 [Commented]

> Username: mzimbres  
> Created_at: 2026-02-22 10:15:27 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/redis/pull/390#pullrequestreview-3837207197  

> Username: mzimbres  
> Created_at: 2026-02-22 10:15:27 UTC  
> Url: https://github.com/boostorg/redis/pull/390#discussion_r2837520359  

My only concern with this PR is that this push parser skips non-push messages silently. This might make it impossible to understand user issues. As we iterate over the messages, would it be possible to the iterator to push a reference to the skipped nodes into the push_parser. then we could at least log them after the loop if necessary  
  
```cpp  
      auto parser = push_parser(resp.value());  
      for (push_view elem : parser) {  
           ...  
      }  
      for (auto node : parser.get_skipped()) {  
           ...  
      }  
```  
  
We could provide a macro to simplify that logging.


---

## Comment 2

> Username: mzimbres  
> Created_at: 2026-02-22 10:16:53 UTC  
> Url: https://github.com/boostorg/redis/pull/390#issuecomment-3940645691  

Good job, I think we should not delay this anymore as it is very hard to process push without that.

---

## Comment 3

> Username: anarthal  
> Created_at: 2026-02-23 11:11:03 UTC  
> Url: https://github.com/boostorg/redis/pull/390#issuecomment-3944131961  

Hm, storing references would require allocating. I could add an extended API to the parser (or even create a similar `push_parser_ext`) that also yields skipped messages. For instance:  
  
```cpp  
struct push_view { /* as is today */ };  
using push_or_skipped = variant<  
   push_view, // a valid push  
   span<const resp3::node_view> // skipped message  
>;  
class push_parser_ext {  
   // value type is push_or_skipped  
};  
```  
  
I don't think we can make it to this release though (needs to be on master for this Wednesday). So if you're okay with it, I'll merge it as-is, and think of adding this for next release.  
  
I also hope to implement #387 so we can completely get rid of errors in the push buffer, making this issue less important.

---
