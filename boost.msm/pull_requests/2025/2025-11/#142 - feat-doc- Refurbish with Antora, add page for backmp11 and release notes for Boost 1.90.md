# #142 feat/doc: Refurbish with Antora, add page for backmp11 and release notes for Boost 1.90 [Merged]

> Username: chandryan  
> Created at: 2025-11-02 17:46:57 UTC  
> Updated at: 2025-11-10 20:51:32 UTC  
> Merged at: 2025-11-04 19:43:37 UTC  
> Closed at: 2025-11-04 19:43:38 UTC  
> Url: https://github.com/boostorg/msm/pull/142  

Provided a refurbished documentation based on Antora. Added a page for backmp11 and release notes for the upcoming boost 1.90 release.  
  
The documentation website is structured in the following way:  
- The headers level 1 are shown in the left navigation bar, the second levels are additionally shown for main chapters of the documentation that are comparably big  
- There is an additional right navigation bar for sub-headers within a page  
  
  
Summary of other applied changes:  
- Set up a documentation build with Antora as described in the [Boost Developers Guide](https://www.boost.org/doc/contributor-guide/docs/antora.html)  
- Split msm.adoc into multiple adoc files, with a structure similar to the existing TOC  
- Moved images and examples into the source folder for the Antora doc build as required by Antora  
  
Related to https://github.com/boostorg/msm/issues/104  
  
**Once this PR is merged, a second PR is required to finalze the migration to Antora by adding MSM here: https://github.com/boostorg/website-v2-docs/blob/83ec3f1167a4200192af8b96e1df8768419c0715/libs.playbook.yml#L74**

---
