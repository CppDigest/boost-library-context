# #218 Add support for GNU's named variadics for macros. [Open]

> Username: jwnhy  
> Created at: 2024-09-08 16:14:20 UTC  
> Updated at: 2024-09-15 10:04:42 UTC  
> Url: https://github.com/boostorg/wave/pull/218  

As mentioned in #164, named_variadics like `#define(x...) x` is widely used in projects like the Linux kernel.  
  
This PR adds support for this feature.  
  
- Options and exceptions.  
This PR adds a `BOOST_WAVE_SUPPORT_GNU_NAMED_VARIADICS_PLACEMARKERS` and   
`support_option_named_variadics = 0x100000`.  
  
When the macro is **enabled**, but the runtime option is **not**, it throws `bad_define_statement_named_va_args`, to remind the user to enable the option.  
When the macro is **disabled**, it just reports `ill_formed.`  
  
> I haven't figured out a way to inform the user that he/she needs to enable the macro and recompile for this feature to work...  
  
- Lexer part.  
I modified the lexer so it accept construct like `(ch_p(T_IDENTIFIER) >> *ppsp >> ch_p(T_ELLIPSIS))`, then, I used `token_node_d` to *compress* all these node into one, and `access_node_d` to modify the node information to `T_GNU_NAMED_VARIADIC`.  
  
This helps deal with later replacement and expansion.  
> Keeping all the nodes makes `(x, y...)` and `(x, y, ...)` indistinguishable in the later phases, as the `T_COMMA` does not generate any node.  
> Unless we re-visit the original token seq in the later process,which leads to an egg-chicken problem.  
  
- Expansion part.  
I re-use most of the original codes.  
  
- Testing  
I have run some basic examples to ensure this feature works as expected.  
The original test suite also passed, except for the one failure `t_5_007.cpp` that was introduced in #214, which I guess it's my local `libc` issue.  
  
  
Let me know if there is any issue.

---

## Comment 1

> Username: jefftrull  
> Created_at: 2024-09-15 05:05:53 UTC  
> Url: https://github.com/boostorg/wave/pull/218#issuecomment-2351373457  

I haven't had an opportunity to review the code yet (currently traveling) but this will definitely need tests.  
  
You can either add to one of the existing variadic macro tests, or create a new one (particularly if different command line options are required, e.g. whatever activates the gcc extensions mode).  
  
AFAIK the best documentation on the various test features are found in the test driver code [here](https://github.com/boostorg/wave/blob/ef14767aec712d6d342dc598fbb1432236d930c8/test/testwave/testwave_app.cpp#L431). I often start by finding a similar test to the one I need to write under `test/testwave/testfiles`.

---

## Comment 2

> Username: jwnhy  
> Created_at: 2024-09-15 10:04:41 UTC  
> Url: https://github.com/boostorg/wave/pull/218#issuecomment-2351509832  

> I haven't had an opportunity to review the code yet (currently traveling) but this will definitely need tests.  
>   
> You can either add to one of the existing variadic macro tests, or create a new one (particularly if different command line options are required, e.g. whatever activates the gcc extensions mode).  
>   
> AFAIK the best documentation on the various test features are found in the test driver code [here](https://github.com/boostorg/wave/blob/ef14767aec712d6d342dc598fbb1432236d930c8/test/testwave/testwave_app.cpp#L431). I often start by finding a similar test to the one I need to write under `test/testwave/testfiles`.  
  
Sure, thank you for the advice, sorry I am occupied by some other tasks.  
  
Will find a time to add some tests.

---
