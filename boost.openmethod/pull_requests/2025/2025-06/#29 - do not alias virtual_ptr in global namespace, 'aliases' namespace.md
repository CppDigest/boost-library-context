# #29 do not alias virtual_ptr in global namespace, 'aliases' namespace [Merged]

> Username: jll63  
> Created at: 2025-06-14 16:18:04 UTC  
> Updated at: 2025-06-15 20:57:25 UTC  
> Merged at: 2025-06-14 17:28:30 UTC  
> Closed at: 2025-06-14 17:28:30 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29  



---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-06-14 16:23:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/29#pullrequestreview-2928470864  

## Pull Request Overview  
  
This PR aims to avoid polluting the global namespace with the `virtual_ptr` alias and instead centralize aliases under `boost::openmethod::aliases`. It adds individual `using boost::openmethod::virtual_ptr;` directives in most examples, updates a couple of files to use the `aliases` namespace for other aliases, and fully qualifies `virtual_ptr` in headers.  
  
- Added `using boost::openmethod::virtual_ptr;` globally in multiple example files  
- Switched select files to use `using namespace boost::openmethod::aliases;` for other aliases  
- Qualified `virtual_ptr` with its full namespace in header files  
  
### Reviewed Changes  
  
Copilot reviewed 27 out of 27 changed files in this pull request and generated 15 comments.  
  
<details>  
<summary>Show a summary per file</summary>  
  
| File                                          | Description                                                  |  
|-----------------------------------------------|--------------------------------------------------------------|  
| examples/synopsis.cpp                         | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/slides.cpp                           | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/next.cpp                             | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/matrix.cpp                           | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/hello_world.cpp                      | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/headers_namespaces/main.cpp          | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/headers_namespaces/dog.hpp            | Fully qualifies `virtual_ptr` in macro argument              |  
| examples/headers_namespaces/dog.cpp            | Fully qualifies `virtual_ptr` in macro argument              |  
| examples/headers_namespaces/cat.cpp            | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/headers_namespaces/animal.hpp         | Fully qualifies `virtual_ptr` in macro argument              |  
| examples/friendship_across_namespaces.cpp     | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/friendship.cpp                       | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/deferred_custom_rtti.cpp             | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/default_error_handler.cpp            | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/custom_rtti.cpp                      | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/asteroids.cpp                        | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/ast_unique_ptr.cpp                   | Switches to `using namespace boost::openmethod::aliases;`    |  
| examples/ast.cpp                              | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/adventure.cpp                        | Introduces global `using boost::openmethod::virtual_ptr;`    |  
| examples/accept_no_visitors.cpp               | Switches to `using namespace boost::openmethod::aliases;`    |  
</details>

📁 examples/synopsis.cpp

```diff
  29 | #include <boost/openmethod/compiler.hpp>
  30 | 
  31 |+ using boost::openmethod::virtual_ptr;
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:10 UTC  
> Updated_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016003  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.  
```diff  
-using boost::openmethod::virtual_ptr;  
+// Removed the using directive to avoid aliasing in the global namespace.  
```


📁 examples/slides.cpp

```diff
 134 | 
 135 | using namespace nodes;
 136 |+ using boost::openmethod::virtual_ptr;
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:11 UTC  
> Updated_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016007  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.


📁 examples/next.cpp

```diff
  13 | #include <boost/openmethod/compiler.hpp>
  14 | 
  15 |+ using boost::openmethod::virtual_ptr;
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:11 UTC  
> Updated_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016008  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.  
```diff  
-using boost::openmethod::virtual_ptr;  
+// Removed the using directive for boost::openmethod::virtual_ptr  
```


📁 examples/matrix.cpp

```diff
  17 | using std::string;
  18 | 
  19 |+ using boost::openmethod::virtual_ptr;
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:12 UTC  
> Updated_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016010  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.  
```diff  
-using boost::openmethod::virtual_ptr;  
```


📁 examples/hello_world.cpp

```diff
  32 | #include <boost/openmethod.hpp>
  33 | 
  34 |+ using boost::openmethod::virtual_ptr;
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:12 UTC  
> Updated_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016011  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.


📁 examples/headers_namespaces/main.cpp

```diff
   7 | #include "dog.hpp"
   8 | 
   9 |+ using boost::openmethod::virtual_ptr;
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:12 UTC  
> Updated_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016014  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.  
```diff  
-using boost::openmethod::virtual_ptr;  
+// Removed the using directive for boost::openmethod::virtual_ptr  
```


📁 examples/headers_namespaces/cat.cpp

```diff
   6 | #include "cat.hpp"
   7 | 
   8 |+ using boost::openmethod::virtual_ptr;
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:13 UTC  
> Updated_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016017  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.


📁 examples/friendship_across_namespaces.cpp

```diff
  10 | #include <boost/openmethod.hpp>
  11 | 
  12 |+ using boost::openmethod::virtual_ptr;
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:13 UTC  
> Updated_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016019  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.  
```diff  
-using boost::openmethod::virtual_ptr;  
+// Removed the global using directive for boost::openmethod::virtual_ptr  
```


📁 examples/friendship.cpp

```diff
  10 | #include <boost/openmethod.hpp>
  11 | 
  12 |+ using boost::openmethod::virtual_ptr;
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:13 UTC  
> Updated_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016021  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.  
```diff  
-using boost::openmethod::virtual_ptr;  
+// Removed the using directive for boost::openmethod::virtual_ptr to avoid global namespace pollution.  
```


📁 examples/deferred_custom_rtti.cpp

```diff
 132 | #include <boost/openmethod/compiler.hpp>
 133 | 
 134 |+ using boost::openmethod::virtual_ptr;
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:13 UTC  
> Updated_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016024  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.


📁 examples/default_error_handler.cpp

```diff
  11 | #include <boost/openmethod/compiler.hpp>
  12 | 
  13 |+ using boost::openmethod::virtual_ptr;
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:14 UTC  
> Updated_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016025  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.  
```diff  
-using boost::openmethod::virtual_ptr;  
+// Removed the using directive for boost::openmethod::virtual_ptr  
```


📁 examples/custom_rtti.cpp

```diff
  78 | #include <boost/openmethod/compiler.hpp>
  79 | 
  80 |+ using boost::openmethod::virtual_ptr;
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:14 UTC  
> Updated_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016026  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.


📁 examples/asteroids.cpp

```diff
  10 | #include <boost/openmethod/compiler.hpp>
  11 | 
  12 |+ using boost::openmethod::virtual_ptr;
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:14 UTC  
> Updated_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016029  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.  
```diff  
-using boost::openmethod::virtual_ptr;  
+// Removed the using directive for boost::openmethod::virtual_ptr  
```


📁 examples/ast.cpp

```diff
  14 | 
  15 |+ using boost::openmethod::virtual_ptr;
  16 |+ 
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:14 UTC  
> Updated_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016032  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.  
```diff  
  
```


📁 examples/adventure.cpp

```diff
  12 | 
  13 |+ using boost::openmethod::virtual_ptr;
  14 |+ 
```

> Username: Copilot  
> Created_at: 2025-06-14 16:23:15 UTC  
> Url: https://github.com/boostorg/openmethod/pull/29#discussion_r2147016037  

Avoid aliasing `virtual_ptr` directly in the global namespace; consider using `using namespace boost::openmethod::aliases;` or fully qualify `boost::openmethod::virtual_ptr` where needed.  
```diff  
  
```


---
