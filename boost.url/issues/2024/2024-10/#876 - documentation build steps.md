# #876 - documentation build steps [Closed]

> Username: sdarwin  
> Created at: 2024-10-31 13:11:08 UTC  
> Updated at: 2026-02-04 00:23:29 UTC  
> Closed at: 2026-02-03 23:32:15 UTC  
> Url: https://github.com/boostorg/url/issues/876  

With boost library documentation there are two factors that are both important:  
  
- that the library docs can be built for any individual library, relatively conveniently, simply, directly.  
  
- that the library docs can be compiled by release-tools and published in full release archives.  
  
The difficulties with antora docs in boostorg/url are:  
  
- https://gitlab.com/antora/antora/-/issues/895 "Document generation fails for Git Submodules".  Since boostorg uses submodules, developers out in the world will encounter this. Attempting to add CI jobs also ran into this problem.  
  
- The build script "build_antora.sh" is complicated, and has a large number of dependencies, including the fact it downloads "all of boost". And npm modules.    
  
- It's not standardized. It's not the same method as other libs, which in boost is helpful.  
  
An individual lib contributor would not seem to benefit from a "build_antora.sh" system since it's more complex and more work. The Jamfile method of building one library is simple, basic, quick, convenient.  
  
Here is an idea:    
  
Since Antora is based on AsciiDoc at some level, compose the docs for boostorg/url and other similar libaries using AsciiDoc and Jamfiles, and build them as normal, for each library. For each individual library, nothing changes, and it doesn't know about Antora.    
  
Then, in boostorg/release-tools, collate and compile any discovered AsciiDoc libs into an antora collection. This may involve generating playbooks and dynamically modifying the text content of each library doc.    Well, this is just an idea and I do not know if it would be feasible.

---

## Comment 1

> Username: sdarwin  
> Created at: 2024-10-31 13:48:15 UTC  
> Url: https://github.com/boostorg/url/issues/876#issuecomment-2449887326  

Upon further consideration, maybe this idea is misdirected - if Antora is providing many benefits that cannot be achieved otherwise.  And so, whatever the disadvantages, it is still worthwhile to use Antora for one library such as boostorg/url.    
Nevertheless, the issues do exist. antora doesn't support submodules.  boost depends on submodules.

---

## Comment 2

> Username: alandefreitas  
> Created at: 2024-10-31 18:00:21 UTC  
> Updated at: 2024-10-31 18:08:10 UTC  
> Url: https://github.com/boostorg/url/issues/876#issuecomment-2450503032  

I agree with the complexity and frustration that new different workflows cause. For instance, iterating all libraries and calling the same command on each is no longer a correct strategy. One similar inconvenience is the BoostBook documentation, which causes similar problems in some ways. But basically, I agree this is painful:  
  
> that the library docs can be built for any individual library, relatively conveniently, simply, directly.  
  
Antora assumes that each library is a component of a larger project that must be built all at once.  
  
Especially now that only Boost.URL is using it, there's the apprehension that other libraries won't adopt it, and then we'll end up with a different workflow just for Boost.URL. On the other hand, at least five other libraries could join it immediately because these are libraries we maintain and would like to transition. If we ever give up entirely on Antora, we can revert Boost.URL too Quickbook+Doxygen+Docca.  
  
> https://gitlab.com/antora/antora/-/issues/895 "Document generation fails for Git Submodules". Since boostorg uses submodules, developers out in the world will encounter this. Attempting to add CI jobs also ran into this problem.  
  
This issue doesn't reflect our design. `boostorg/boost` relies on submodules, but the Antora workflow we use doesn't because `boostorg/url` is not a submodule of `boostorg/website-v2-docs`. Each component is just another repository from the POV of the playbook.  
  
I saw that Vinnie made a question there. But Vinnie's question predates our current design.  
  
> The build script "build_antora.sh" is complicated, and has a large number of dependencies, including the fact it downloads "all of boost". And npm modules.  
  
We may need more comments, renaming this script, or better documentation on the workflow. But `build_antora.sh` is not the process to build `Boost.URL` docs. That's why we don't have a Jamfile that just calls it. It's just a small custom script I wrote for generating the preview in PRs without depending on `boostorg/website-v2-docs`.   
  
The process to build Boost.URL docs is running `libdoc.sh` in website-v2-docs, that generates the documentation for all antora libraries. And `libdoc.sh` is just 1) checking if the dependencies are available and 2) running `npx antora`.   
  
Also, `build_antora.sh` builds a playbook that contains an extension that generates the reference. The parameters of this extension needs subset of Boost Boost.URL depends on to be able to compile the code.   
  
However, the extension doesn't need all of Boost. It just needs Boost in `BOOST_SRC_DIR`. It could be only the dependencies of Boost.URL. It only downloads all of Boost if `BOOST_SRC_DIR` isn't set.  
  
> It's not standardized. It's not the same method as other libs, which in boost is helpful.  
  
Yes. Having the same method for all libraries is useful. And we considered that in the trade-offs. There are also benefits, such as the relationship between libraries that opt-in, being based on asciidoc, the UI bundles, navigation, and many ways to customize it.  
  
It's not standardized in the sense that it's a new workflow (not the same as other libs). It's a proposal for a new standard worth adopting because of its benefits. The contributor guide in `website-v2-doc` has a section describing the common procedure for all libraries. The new standard becomes "the old procedure or this new procedure".  
  
People may be willing to reevaluate the trade-offs and choose some other tool to replace Antora. But Antora does offer extra features that can't be replicated easily with just Asciidoc.  
  
The other points you mention after this list assume all points are correct and interrelated:  
  
- "An individual lib contributor would not seem to benefit from a "build_antora.sh"" assumes 2  
- "Abandoning Antora completely for Asciidoc (and maybe collating the documentation later)": It solves the problem by removing the solution. People have to agree that they don't want this solution anymore. Antora provides much more than Asciidoc.

---

## Comment 3

> Username: sdarwin  
> Created at: 2024-10-31 18:18:37 UTC  
> Url: https://github.com/boostorg/url/issues/876#issuecomment-2450544491  

> The process to build Boost.URL docs is running libdoc.sh in website-v2-docs, that generates the documentation for all antora libraries.  
  
Imagine the goal is for widespread adoption of this method.  Two hundred libraries are on-board now.  I am the author of boostorg/leaf.   I am testing locally, and want to iterate: edit the docs, build and view them, edit the docs, build and view them, repeat.  
  
Can this be accurate:  "The process to build Boost.LEAF docs is running libdoc.sh in website-v2-docs, that generates the documentation for all antora libraries."  
  
It seems unbelievable, to build 200 libraries each time, to require all of them to be checked out locally, how long will it take for everything to compile, and what if another library has a bug, can I build leaf?

---

## Comment 4

> Username: alandefreitas  
> Created at: 2024-10-31 19:14:16 UTC  
> Updated at: 2024-10-31 19:16:01 UTC  
> Url: https://github.com/boostorg/url/issues/876#issuecomment-2450640301  

Yes, I agree. Libraries not being independent is a price for the benefit of cross-references, etc. And I agree the price is high in many ways. I am also open to removing Antora completely and using another tool where documentation is always independent if other people agree. Or maybe some people would use multiple Antora playbooks with a single component representing their libraries. Then `website-v2-docs` would only be about `sitedoc.sh`. In practice, that's a matter of convincing other people through other means (Slack). Giving up on the Antora workflow with a single playbook and one component per library in the Boost workflow is above the scope of this repository.  
  
Still, as far as I have any influence over the process, I'm thinking of a better approach for when more libraries use it. What `build_antora.sh` and the local playbook do is render a version of the playbook documentation without the other components. Everything else in the playbook is the same. So it's easy to automate a workflow to get the original `libdoc.sh` from `website-v2-docs`, remove other components, and just leave the one being tested. We can remove the other components with an extension or by just programmatically changing the playbook.  
  
The fact that this is possible without much loss (we could lose some cross-references) is evidence we could have one playbook per library. But again, that goes back to convincing other people through other means so we can undo the changes in `release-tools`.

---

## Comment 5

> Username: sdarwin  
> Created at: 2024-11-04 12:51:22 UTC  
> Updated at: 2024-11-04 13:19:33 UTC  
> Url: https://github.com/boostorg/url/issues/876#issuecomment-2454637567  

The Antora git client "isomorphic-git" is written in javascript.    
From  https://github.com/isomorphic-git/isomorphic-git/issues/1647 there was a proposed feature with a comment:  
  
"Is this really all there is to support working with submodules?"  
  
Meaning that maybe it's not impossible to add submodule capabilities to "isomorphic-git".  
  
Also:  
"I don't think anyone is working on this. This project is community driven which means that any changes to the project are done by individual contributors. There is no main author that writes all the code, he left the project. Do you want to contribute?"   They are open to contributions.  
  
---  
  
To make a note, the antora developers recommended Playbook Providers to support per-repo previews.  That is similar to the strategy already mentioned.   "The same thing could be done for any other build tool, so if nothing else, this is a reference for the idea."    
https://docs.antora.org/gradle-plugin/latest/playbook-provider/    
https://docs.antora.org/maven-plugin/latest/playbook-provider/

---

## Comment 6

> Username: alandefreitas  
> Created at: 2026-02-03 23:32:15 UTC  
> Url: https://github.com/boostorg/url/issues/876#issuecomment-3844363792  

I believe the final trade-off is +-:  
  
- One playbook per module: The author has more control over the script. The test script is identical to the deployment. We can reuse the pattern with b2.  
- One playbook for all modules: Cross-references. No duplicated resources. No need to update dependencies per module.  
  
Over time, most authors have been choosing one playbook per module because it's a better deal for them. Current direction for Boost.URL is to use the global playbook, though. I'm personally open to changing it. I'll close it as not planned unless something changes.

---

## Comment 7

> Username: sdarwin  
> Created at: 2026-02-04 00:13:06 UTC  
> Url: https://github.com/boostorg/url/issues/876#issuecomment-3844489919  

I haven't worked on this exact issue recently, but had an idea about it. A sub-component of the problem was "download all of boost".  Whenever that happens perhaps it could be replaced with  https://github.com/boostorg/boostdep/blob/develop/depinst/depinst.py    
  
Other doc build scripts use `depinst.py` to install a few boost submodules, rather than all of them.

---

## Comment 8

> Username: alandefreitas  
> Created at: 2026-02-04 00:23:29 UTC  
> Url: https://github.com/boostorg/url/issues/876#issuecomment-3844528016  

Yes. If using the reference extension, all you need is to set `BOOST_SRC_DIR`. All transitive boost dependencies are already available in the release environment and in CI. So it's not such a big issue in these environments.   
  
Exactly. The only environment where boost might not be available at `BOOST_SRC_DIR,` and the extension will actually download it, is when generating documentation previews. In this case, we can download the dependencies depinst and set BOOST_SRC_DIR. The build_antora.sh script could do that when BOOST_SRC_DIR is unset.  
  
Of course, this adds extra complexity, but it is already accounted for in the trade-off above. It was an explicit internal decision.
