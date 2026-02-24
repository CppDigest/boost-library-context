# #14 - [peer-review] Provide copy-and-paste-ready examples, syntax highlighting, and visuals for parameters vs. false-positive rate [Closed]

> Username: Becheler  
> Created at: 2025-05-31 10:52:07 UTC  
> Updated at: 2025-06-24 13:29:45 UTC  
> Closed at: 2025-06-24 13:29:45 UTC  
> Url: https://github.com/boostorg/bloom/issues/14  

# Description  
  
Two reviewers, Ruben Perez and Ivan Matek, noted that the documentation’s code examples and visual aids could be improved for readability and clarity. Below are relevant excerpts and suggested improvements:  
  
Ruben Perez, May 21  
- > Examples in the docs would benefit from syntax highlighting. You can use asciidoc [source,cpp] blocks to achieve it.  
- > I’ve found the examples more difficult to read than what I’d have liked. Some more comments would be useful.  
- > I’d try to make code snippets as close to real code as possible. That is, I’d try to avoid things like using filter = boost::bloom::filter<std::string, ...>;, in favor of something that the user may copy to their code.”  
  
Ivan Matek, May 17  
- > It may be just me, but I believe Filter Definition chapter would benefit from visual example, in particular how is BucketSize related to subarray size.  
- > Regarding choosing optimal value: In the example we use 10M elements. From what I see they are unused in our computations till the end so I would rewrite: ’Suppose we plan to insert 10M elements and want to keep the FPR at 10⁻⁴. The chart gives us five possibilities:’ to something like ’Suppose we plan to insert 10M elements and want to keep the FPR at 10⁻⁴. Looking up FPR in the chart (this step is not dependent on number of elements) gives us five possibilities:’  
  
## Suggested Action List  
  
1. **Enable Syntax Highlighting for Code Examples**  
   - Wrap all C++ code blocks in appropriate syntax highlighting tags (e.g., `[source,cpp]` or ```cpp).  
  
2. **Add Inline Comments to Examples**  
   - Annotate each code snippet with brief comments explaining key steps (e.g., creating a filter, inserting elements, checking membership).  
  
3. **Provide Copy-and-Paste-Ready Snippets**  
   - Replace placeholder `using filter = boost::bloom::filter<…>;` aliases with full, compilable declarations.  
   - Ensure every example can be copied directly into a user’s project without modification.  
  
4. **Visualize `BucketSize` vs. Subarray Size**  
   - Create a simple diagram (ASCII art, table, or image) illustrating how the `BucketSize` parameter maps to subarray size in the Filter Definition chapter.  
  
5. **Clarify “Choosing Optimal Value” Text**  
   - Rewrite the paragraph to emphasize that looking up FPR in the chart is independent of the element count.    
  
## Requested Outcome  
  
- [ ] All code examples in the documentation should be wrapped in syntax highlighting and include sufficient inline comments to guide readers.  
- [ ] Examples must be immediately copy-paste-ready (no placeholder typedefs).  
- [ ] The Filter Definition chapter should feature a visual aid illustrating BucketSize vs. subarray size.  
- [ ] The “choosing optimal value” section should be rewritten for clarity, explicitly stating which steps depend on element count and which do not.

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-06-13 08:19:01 UTC  
> Url: https://github.com/boostorg/bloom/issues/14#issuecomment-2969501924  

* [Added syntax highlighting to code snippets](https://github.com/joaquintides/bloom/commit/05fd53b513f5ae32a9fa4ebadfdfd9e5de5aeff3)  
* [Made first code snippet self-contained](https://github.com/joaquintides/bloom/commit/b670f6ec52b4b781dfd5a00a1e57d14159492f0b)  
* [Added diagram about overlapping vs. non-overlapping subarrays](https://github.com/joaquintides/bloom/commit/b265941fc4a28836f96b41a164ef19156443323f)  
* [Stressed config chart x axis is capacity/num elements rather than plain capacity](https://github.com/joaquintides/bloom/commit/3c1dc6d26b8f520bec15d4ff64983471134feb45)

---

## Comment 2

> Username: Becheler  
> Created at: 2025-06-24 13:29:45 UTC  
> Url: https://github.com/boostorg/bloom/issues/14#issuecomment-3000503123  

Thank you for adding all of those ! :)
