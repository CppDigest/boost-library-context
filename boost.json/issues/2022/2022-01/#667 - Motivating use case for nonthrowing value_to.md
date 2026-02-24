# #667 - Motivating use case for nonthrowing value_to [Closed]

> Username: pdimov  
> Created at: 2022-01-05 15:55:06 UTC  
> Updated at: 2022-10-14 17:13:45 UTC  
> Closed at: 2022-10-14 17:13:45 UTC  
> Url: https://github.com/boostorg/json/issues/667  

Suppose that a user wants to represent `variant<T1, T2, T3>` as just the active alternative; that is, when the variant contains `T1`, its `value_from` function just forwards to T1's `value_from`, and similarly for T2 and T3. When converting from a `value`, it tries the alternatives in order, and uses the first one that matches. (https://godbolt.org/z/GqKPYx4Tq)  
  
This occurs in practice when the JSON schema is externally specified and the types of some values can vary. For instance, Boost's `meta/libraries.json` allows both a string and an array of strings in the `authors` and `maintainers` fields. This can be represented by `variant<string, vector<string>>`.  
  
The solution in the above CE link works, but it throws `sizeof...(T) / 2` times on average on each `value_to` call, which kills performance. It would be much better if a nonthrowing alternative of `value_to` existed that returned the parse/type errors using `error_code`. In fact, the code above emulates this nonthrowing alternative with the `try_value_to` function.  
  
There are still few users for the `tag_invoke` mechanism so switching to a nonthrowing support function _now_ won't break much existing code, although the longer we wait, the less true this becomes.
