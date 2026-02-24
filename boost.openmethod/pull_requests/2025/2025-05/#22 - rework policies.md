# #22 rework policies [Merged]

> Username: jll63  
> Created at: 2025-05-17 19:30:57 UTC  
> Updated at: 2025-06-01 17:52:58 UTC  
> Merged at: 2025-05-19 22:45:57 UTC  
> Closed at: 2025-05-19 22:45:57 UTC  
> Url: https://github.com/boostorg/openmethod/pull/22  



---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-05-17 19:32:23 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/22#pullrequestreview-2848381435  

## Pull Request Overview  
  
This PR renames the default policy concept to “default_registry” and updates related macros, aliases, and facet names to reflect this change across documentation and code examples. Additionally, it streamlines facet names by changing `error_output`→`output`, `trace_output`→`trace`, and `has_facet`→`has`.  
  
- Rename `default_policy` references and `BOOST_OPENMETHOD_DEFAULT_POLICY` macro to `default_registry`/`BOOST_OPENMETHOD_DEFAULT_REGISTRY`.  
- Update facet names: `error_output`→`output`, `trace_output`→`trace`.  
- Adjust member naming: `has_facet`→`has`.  
  
### Reviewed Changes  
  
Copilot reviewed 61 out of 61 changed files in this pull request and generated 1 comment.  
  
<details>  
<summary>Show a summary per file</summary>  
  
| File                                     | Description                                                        |  
| ---------------------------------------- | ------------------------------------------------------------------ |  
| doc/virtual_ptr_alt.adoc                 | Updated nested `default_policy`→`default_registry` reference       |  
| doc/virtual_ptr.adoc                     | Changed default policy template parameters to default_registry     |  
| doc/vectored_error_handler.adoc          | Renamed `error_output` facet usage to `output`                     |  
| doc/use_classes.adoc                     | Updated default policy alias to default_registry                   |  
| doc/type_hash.adoc                       | Renamed `trace_output` facet usage to `trace`                      |  
| doc/trace_output.adoc                    | Renamed facet struct `trace_output`→`trace` and updated description|  
| doc/reference.adoc                       | Updated include directives for default_registry and facets         |  
| doc/policies.adoc                        | Renamed default policy alias and facets                            |  
| doc/overview.adoc                        | Updated overview to reference default_registry and new facet names |  
| doc/method.adoc                          | Changed method default policy template to default_registry         |  
| doc/initialize.adoc                      | Updated initialize/finalize default policy to default_registry     |  
| doc/html/index.html                      | Reflected default_registry, facet, and member name changes         |  
| doc/error_output.adoc                    | Renamed `error_output` facet to `output`                           |  
| doc/custom_rtti.adoc                     | Updated default policy macro to default_registry                   |  
| doc/basic_trace_output.adoc              | Renamed facet inheritance and description                          |  
| doc/basic_policy.adoc                    | Changed `has_facet`→`has` and default alias to default_registry    |  
| doc/basic_error_output.adoc              | Updated facet inheritance to `output`                              |  
| doc/BOOST_OPENMETHOD_DEFAULT_POLICY.adoc | Renamed policy documentation to default_registry                   |  
| doc/BOOST_OPENMETHOD_CLASSES.adoc        | Updated default policy reference in macro docs                     |  
| doc/BOOST_OPENMETHOD.adoc                | Changed default policy macro references to default_registry        |  
</details>  
  
  
  
<details>  
<summary>Comments suppressed due to low confidence (1)</summary>  
  
**doc/basic_trace_output.adoc:19**  
* The description refers to `basic_error_output` but this is the `basic_trace_output` component; update the name and clarify it writes trace messages, not error messages.  
```  
`basic_error_output` is an implementation of `trace` that writes error messages to a `RestrictedOutputStream`.  
```  
</details>

📁 doc/BOOST_OPENMETHOD_DEFAULT_POLICY.adoc

```diff
  12 | `<boost/openmethod/core.hpp>`. If it is not defined,
  13 |- `::boost::openmethod::policy::default_policy` is used.
  13 |+ `::boost::openmethod::policy::default_registry` is used.
```

> Username: Copilot  
> Created_at: 2025-05-17 19:32:23 UTC  
> Updated_at: 2025-05-17 19:32:24 UTC  
> Url: https://github.com/boostorg/openmethod/pull/22#discussion_r2094194620  

The qualified namespace is incorrect: `default_registry` is defined in `boost::openmethod`, not `boost::openmethod::policy`. Update to `::boost::openmethod::default_registry`.  
```diff  
-`::boost::openmethod::policy::default_registry` is used.  
+`::boost::openmethod::default_registry` is used.  
```


---
