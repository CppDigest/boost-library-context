# #294 - Regression introduced: value type in context in rule_parser started changing [Closed]

> Username: andreasbuhr  
> Created at: 2025-11-16 08:29:27 UTC  
> Updated at: 2026-02-01 10:49:36 UTC  
> Closed at: 2026-01-31 01:16:35 UTC  
> Url: https://github.com/boostorg/parser/issues/294  

A regression was introduced in https://github.com/boostorg/parser/commit/c674e94c3d409b6ab0ee05429361e8b50fe30320.  
  
Since that commit, the `call` method of `rule_parser` which does take an Attribute by reference uses the type of that Attribute to construct the context.  
Since that commit, `_val(ctx)` in the actions can be whatever the consumer passes as `Attribute` to `call()`.  
But the value in the context, and thereby `_val(ctx)` in the actions, should always be the attribute of the rule_parser.  
  
This becomes a problem when the rule_parser is used in an opt_parser. The opt_parser relies on the fact that it can pass its optional into the underlying parser, because the `std::optional<Foo>` can be constructed from `Foo`. But then therein, I suddenly get `std::optional<Foo>` as `_val(ctx)` in my actions, where I assume it would be just a Foo.  
  
I'll create a pull request with a reproducer.

---

## Comment 1

> Username: andreasbuhr  
> Created at: 2025-11-16 08:33:23 UTC  
> Url: https://github.com/boostorg/parser/issues/294#issuecomment-3538410196  

Reproducer in https://github.com/boostorg/parser/pull/295

---

## Comment 2

> Username: tzlaine  
> Created at: 2025-11-17 06:38:09 UTC  
> Updated at: 2025-11-17 06:38:47 UTC  
> Url: https://github.com/boostorg/parser/issues/294#issuecomment-3540202393  

Oof.  This turns out to be a very deep issue.  Within a semantic action applied to a rule, we want `_val(ctx)` always to produce a value of the rule's attribute type.  Well, unless the user has opted into the out-param system, in which case, `_val` should probably produce whatever value has been given to the associated rule.  That's not what's happening here though -- the rules are being used in a top-level parse that returns a result, not a parse that fills in a result.  However, the rules are being invoked with an out-param as an implementation detail within the parser.  
  
In order to fix this, it turns out that I need to change the way rule parers are invoked throughout the entire library, and I need to condition how they are called on the presence or absence of an out-param at the top level.  Or at least I think that's right.  
  
That's major surgery.  So, while I intend to fix this, it's too late for the upcoming Boost release.  
  
Thanks for the repro; it helped a lot.

---

## Comment 3

> Username: andreasbuhr  
> Created at: 2025-11-17 09:31:51 UTC  
> Url: https://github.com/boostorg/parser/issues/294#issuecomment-3540788519  

Thanks for looking into it @tzlaine.

---

## Comment 4

> Username: tzlaine  
> Created at: 2026-01-31 01:19:14 UTC  
> Url: https://github.com/boostorg/parser/issues/294#issuecomment-3827084562  

Thanks again for reporting this; it definitely needed fixing.  It was the hardest single thing I think I've fixed in the library ever.  All that stuff I said above about the 'major surgery' approach was wrong, and the fix was much smaller.  I was super confused the whole time though because of the nature of the symptoms.  Glad to have this one behind me!  😄

---

## Comment 5

> Username: andreasbuhr  
> Created at: 2026-02-01 10:49:36 UTC  
> Url: https://github.com/boostorg/parser/issues/294#issuecomment-3830869459  

That's great. Thanks a lot.
