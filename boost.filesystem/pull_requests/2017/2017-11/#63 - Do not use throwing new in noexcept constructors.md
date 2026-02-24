# #63 Do not use throwing new in noexcept constructors [Closed]

> Username: pdimov  
> Created at: 2017-11-22 18:35:33 UTC  
> Updated at: 2018-11-24 16:01:05 UTC  
> Closed at: 2018-11-24 16:01:04 UTC  
> Url: https://github.com/boostorg/filesystem/pull/63  

This resolves issue #58.

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2018-03-02 16:19:56 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/63#pullrequestreview-100829010  

📁 include/boost/filesystem/operations.hpp

```diff
1187 |+ 
1188 |+         m_imp->m_options = opt;
1189 |+         m_imp->m_stack.push(directory_iterator(dir_path, ec));
```

> Username: Lastique  
> Created_at: 2018-03-02 16:19:56 UTC  
> Updated_at: 2018-03-02 16:20:57 UTC  
> Url: https://github.com/boostorg/filesystem/pull/63#discussion_r171891873  

This `push` can still throw, doesn't it? `directory_iterator` constructor also can throw. Maybe it's better to wrap the constructor and initialization list in `try`/`catch` instead? The same with the other constructor.


---

## Comment 2

> Username: jeking3  
> Created_at: 2018-07-17 01:53:08 UTC  
> Url: https://github.com/boostorg/filesystem/pull/63#issuecomment-405434030  

@pdimov this needs a follow-up from you.

---
