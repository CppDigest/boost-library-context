# #276 Implement feature relevance. [Closed]

> Username: swatanabe  
> Created at: 2018-01-04 18:08:52 UTC  
> Updated at: 2021-10-02 21:00:08 UTC  
> Closed at: 2018-01-16 19:24:13 UTC  
> Url: https://github.com/boostorg/build/pull/276  

This is a long-standing issue that I've finally gotten around to.  
  
The benefits:  
- Reduce excessively long target paths.  
- Allows the removal of many special case workarounds that apply similar behavior to specific tools.  
- New features can be added without worrying about their effects on unrelated targets.  The current behavior significantly restricts how features can be used.  
  
Backwards compatibility issues:  
- Some user defined generators will need to be fixed.  
- Indirect conditionals may need to specify relevance manually (Failure to do so may result in "name clash" errors.  Note that most indirect conditionals manipulate feature which are already relevant, so there may not be any actual problems)  
- User defined actions that read properties manually need to call toolset.uses-features.  
- A main-target may span multiple directories.  Any code that assumes that two targets are in the same directory, without guaranteeing that they have the same set of relevant features, may break randomly.  
  
The option --ignore-relevance will restore the old behavior.  This option is only to allow users to work around temporary problems and will be removed in the future.  
  
This is a major design change, so I'm posting it here for review before adding to develop.  If there are no significant objections, I'll merge it in about a week.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2018-01-16 19:24:13 UTC  
> Url: https://github.com/boostorg/build/pull/276#issuecomment-358075497  

This has been merged (I squashed it and added a few more fixes.).

---

## Comment 2

> Username: github-actions[bot]  
> Created_at: 2021-10-02 21:00:07 UTC  
> Url: https://github.com/boostorg/build/pull/276#issuecomment-932819747  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/pulls

---
