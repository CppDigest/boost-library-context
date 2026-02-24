# #6 More thorough pass at introducing internal cross-referencing for Boost.Fiber doc. [Merged]

> Username: nat-goodspeed  
> Created at: 2013-11-08 23:45:30 UTC  
> Updated at: 2016-06-06 11:36:33 UTC  
> Merged at: 2013-11-15 09:14:08 UTC  
> Closed at: 2013-11-15 09:14:08 UTC  
> Url: https://github.com/boostorg/fiber/pull/6  

Linking to a section heading is somewhat fragile: it depends on the current tag names and document organization. If the referenced section is moved to a different level of section nesting, or any of the tags for any containing section change, existing links break.  
  
Linking to a plain [heading] is worse: the link must reference (a normalization of) the heading text. If the wording changes, links break. Worse yet, if a [heading] contains markup or punctuation (e.g. Member function `foo()`) the normalized tag is ridiculously long, almost impossible to predict. We often use both in [heading] text.  
  
For all the above reasons, it's better to explicitly place [#my_anchor] tags and link to them. But it seems unreasonable to have to put two highly redundant QuickBook directives at every [heading], since we use [heading] for every member function.  
  
So we've defined QuickBook templates (macros with arguments) in matched pairs: class_heading and class_link; template_heading and template_link; member_heading and member_link; operator_heading and operator_link; template_member_heading and template_member_link; static_member_heading and static_member_link; function_heading and function_link; ns_function_heading and ns_function_link (for namespace-qualified functions).  
  
Then we went through and pervasively changed most existing [heading] usage to reference one of the [mumble_heading] templates instead. That permits changing `__some_reference__` macros to use the corresponding [mumble_link] templates, so that most `__some_reference__` uses now emit cross-reference links.  
  
Hopefully we won't need further pervasive changes to most such [mumble_heading] instances. Hopefully if we need to tweak them, we can just tweak the template implementations.

---
