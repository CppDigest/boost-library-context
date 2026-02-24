# #30 depinst option to detect cycles [Merged]

> Username: grisumbras  
> Created at: 2025-10-09 06:42:23 UTC  
> Updated at: 2025-10-09 16:33:28 UTC  
> Merged at: 2025-10-09 14:45:13 UTC  
> Closed at: 2025-10-09 14:45:13 UTC  
> Url: https://github.com/boostorg/boostdep/pull/30  

This PR adds an option to depinst to detect cycles for the topmost library. A detected cyle is treated like an error (an exception is raised).

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-10-09 12:00:25 UTC  
> Url: https://github.com/boostorg/boostdep/pull/30#issuecomment-3385545570  

Looks like this doesn't do anything when a cycle is detected, but just crashes with an unhandled exception?

---

## Comment 2

> Username: grisumbras  
> Created_at: 2025-10-09 12:36:11 UTC  
> Url: https://github.com/boostorg/boostdep/pull/30#issuecomment-3385668920  

Yes, my idea was that you add the flag and then your CI jobs fail if you get a cycle. Python will print the exception message which contains the cycle description.

---

## Review 3 [Commented]

> Username: pdimov  
> Created_at: 2025-10-09 12:51:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostdep/pull/30#pullrequestreview-3318821945  

📁 depinst/depinst.py

```diff
 238 |     parser.add_argument( '-g', '--git_args', help="additional arguments to `git submodule update`", default='', action='store' )
 239 |     parser.add_argument( '-u', '--update', help='update <library> before scanning', action='store_true' )
 240 |+     parser.add_argument( '-C', '--detect-cylces', help='detect if <library> has cyclical dependencies', action='store_true' )
```

> Username: pdimov  
> Created_at: 2025-10-09 12:51:13 UTC  
> Url: https://github.com/boostorg/boostdep/pull/30#discussion_r2416692850  

"cycles" is misspelled. It should probably be "--reject-cycles", because "detect" implies just detection, not erroring out. The description should also match the behavior and be "issue an error if <library> has cyclical dependencies".

> Username: grisumbras  
> Created_at: 2025-10-09 12:58:54 UTC  
> Url: https://github.com/boostorg/boostdep/pull/30#discussion_r2416715725  

As an alternative, I can modify it to warn or error-out on cycles, depending on the value of the flag. E.g.  
  
`depinst -C E` -- a cycle will cause an error  
`depinst -C W` -- a cycle will just cause a warning.

> Username: pdimov  
> Created_at: 2025-10-09 13:11:27 UTC  
> Url: https://github.com/boostorg/boostdep/pull/30#discussion_r2416752094  

If our use case is CI, warnings aren't useful, so this is fine as is.  
  
If we want warnings, they should always be printed. The option will just turn them into errors.


---

## Review 4 [Commented]

> Username: pdimov  
> Created_at: 2025-10-09 12:51:28 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/boostdep/pull/30#pullrequestreview-3318822862  

📁 depinst/depinst.py

```diff
 272 |-     scan_module_dependencies( m, x, gm, deps, dirs )
 280 |+     dep_path = []
 281 |+     if args.detect_cylces:
```

> Username: pdimov  
> Created_at: 2025-10-09 12:51:28 UTC  
> Updated_at: 2025-10-09 12:51:29 UTC  
> Url: https://github.com/boostorg/boostdep/pull/30#discussion_r2416693511  

Same misspelling here as well. What are the odds?

> Username: grisumbras  
> Created_at: 2025-10-09 12:53:00 UTC  
> Url: https://github.com/boostorg/boostdep/pull/30#discussion_r2416698160  

Oneshotted by autocompletion.


---

## Comment 5

> Username: pdimov  
> Created_at: 2025-10-09 12:54:29 UTC  
> Url: https://github.com/boostorg/boostdep/pull/30#issuecomment-3385737763  

I'm not entirely convinced of the use case for this option, but I suppose it could be useful in a scenario when a cycle is introduced somewhere and then removed; this can then be added to CI to prevent the problem from reappearing.  
  
Or perhaps you could make a PR to e.g. Geometry with the option added to demonstrate that a cycle is present.

---

## Comment 6

> Username: grisumbras  
> Created_at: 2025-10-09 12:56:58 UTC  
> Url: https://github.com/boostorg/boostdep/pull/30#issuecomment-3385749240  

Well, we can advertise this new option. I can also make the flag to default to True.  
  
Or do you have an idea for an alternative approach?

---

## Comment 7

> Username: pdimov  
> Created_at: 2025-10-09 12:57:47 UTC  
> Url: https://github.com/boostorg/boostdep/pull/30#issuecomment-3385752143  

No, I don't want it to default to True. depinst works perfectly well when cycles are present.

---
