# #181 build(deps): bump fast-xml-parser from 5.3.4 to 5.3.6 in /doc [Merged]

> Username: dependabot[bot]  
> Created at: 2026-02-18 04:38:03 UTC  
> Updated at: 2026-02-18 07:35:08 UTC  
> Merged at: 2026-02-18 07:35:00 UTC  
> Closed at: 2026-02-18 07:35:00 UTC  
> Url: https://github.com/boostorg/msm/pull/181  

Bumps [fast-xml-parser](https://github.com/NaturalIntelligence/fast-xml-parser) from 5.3.4 to 5.3.6.  
<details>  
<summary>Release notes</summary>  
<p><em>Sourced from <a href="https://github.com/NaturalIntelligence/fast-xml-parser/releases">fast-xml-parser's releases</a>.</em></p>  
<blockquote>  
<h2>Entity security and performance</h2>  
<ul>  
<li>Improve security and performance of entity processing  
<ul>  
<li>new options <code>maxEntitySize</code>, <code>maxExpansionDepth</code>, <code>maxTotalExpansions</code>, <code>maxExpandedLength</code>, <code>allowedTags</code>,<code>tagFilter</code></li>  
<li>fast return when no edtity is present</li>  
<li>improvement replacement logic to reduce number of calls</li>  
<li></li>  
</ul>  
</li>  
</ul>  
<p><strong>Full Changelog</strong>: <a href="https://github.com/NaturalIntelligence/fast-xml-parser/compare/v5.3.5...v5.3.6">https://github.com/NaturalIntelligence/fast-xml-parser/compare/v5.3.5...v5.3.6</a></p>  
<h2>v5.3.5</h2>  
<h2>What's Changed</h2>  
<ul>  
<li>Add missing exports to fxp commonjs types by <a href="https://github.com/jeremymeng"><code>@​jeremymeng</code></a> in <a href="https://redirect.github.com/NaturalIntelligence/fast-xml-parser/pull/782">NaturalIntelligence/fast-xml-parser#782</a></li>  
<li>fix: Escape regex char in entity name</li>  
<li>update strnum to 2.1.2</li>  
</ul>  
<h2>New Contributors</h2>  
<ul>  
<li><a href="https://github.com/jeremymeng"><code>@​jeremymeng</code></a> made their first contribution in <a href="https://redirect.github.com/NaturalIntelligence/fast-xml-parser/pull/782">NaturalIntelligence/fast-xml-parser#782</a></li>  
</ul>  
<p><strong>Full Changelog</strong>: <a href="https://github.com/NaturalIntelligence/fast-xml-parser/compare/v5.3.4...v5.3.5">https://github.com/NaturalIntelligence/fast-xml-parser/compare/v5.3.4...v5.3.5</a></p>  
</blockquote>  
</details>  
<details>  
<summary>Changelog</summary>  
<p><em>Sourced from <a href="https://github.com/NaturalIntelligence/fast-xml-parser/blob/master/CHANGELOG.md">fast-xml-parser's changelog</a>.</em></p>  
<blockquote>  
<p><!-- raw HTML omitted -->Note: If you find missing information about particular minor version, that version must have been changed without any functional change in this library.<!-- raw HTML omitted --></p>  
<p><strong>5.3.6 / 2026-02-14</strong></p>  
<ul>  
<li>Improve security and performance of entity processing  
<ul>  
<li>new options <code>maxEntitySize</code>, <code>maxExpansionDepth</code>, <code>maxTotalExpansions</code>, <code>maxExpandedLength</code>, <code>allowedTags</code>,<code>tagFilter</code></li>  
<li>fast return when no edtity is present</li>  
<li>improvement replacement logic to reduce number of calls</li>  
</ul>  
</li>  
</ul>  
<p><strong>5.3.5 / 2026-02-08</strong></p>  
<ul>  
<li>fix: Escape regex char in entity name</li>  
<li>update strnum to 2.1.2</li>  
<li>add missing exports in CJS typings</li>  
</ul>  
<p><strong>5.3.4 / 2026-01-30</strong></p>  
<ul>  
<li>fix: handle HTML numeric and hex entities when out of range</li>  
</ul>  
<p><strong>5.3.3 / 2025-12-12</strong></p>  
<ul>  
<li>fix <a href="https://redirect.github.com/NaturalIntelligence/fast-xml-parser/issues/775">#775</a>: transformTagName with allowBooleanAttributes adds an unnecessary attribute</li>  
</ul>  
<p><strong>5.3.2 / 2025-11-14</strong></p>  
<ul>  
<li>fix for import statement for v6</li>  
</ul>  
<p><strong>5.3.1 / 2025-11-03</strong></p>  
<ul>  
<li>Performance improvement for stopNodes (By <a href="https://github.com/macieklamberski">Maciek Lamberski</a>)</li>  
</ul>  
<p><strong>5.3.0 / 2025-10-03</strong></p>  
<ul>  
<li>Use <code>Uint8Array</code> in place of <code>Buffer</code> in Parser</li>  
</ul>  
<p><strong>5.2.5 / 2025-06-08</strong></p>  
<ul>  
<li>Inform user to use <a href="https://github.com/NaturalIntelligence/fxp-cli">fxp-cli</a> instead of in-built CLI feature</li>  
<li>Export typings  for direct use</li>  
</ul>  
<p><strong>5.2.4 / 2025-06-06</strong></p>  
<ul>  
<li>fix (<a href="https://redirect.github.com/NaturalIntelligence/fast-xml-parser/issues/747">#747</a>): fix EMPTY and ANY with ELEMENT in DOCTYPE</li>  
</ul>  
<p><strong>5.2.3 / 2025-05-11</strong></p>  
<ul>  
<li>fix (<a href="https://redirect.github.com/NaturalIntelligence/fast-xml-parser/issues/747">#747</a>): support EMPTY and ANY with ELEMENT in DOCTYPE</li>  
</ul>  
<p><strong>5.2.2 / 2025-05-05</strong></p>  
<ul>  
<li>fix (<a href="https://redirect.github.com/NaturalIntelligence/fast-xml-parser/issues/746">#746</a>): update strnum to fix parsing issues related to enotations</li>  
</ul>  
<p><strong>5.2.1 / 2025-04-22</strong></p>  
<ul>  
<li>fix: read DOCTYPE entity value correctly</li>  
<li>read DOCTYPE NOTATION, ELEMENT exp but not using read values</li>  
</ul>  
<p><strong>5.2.0 / 2025-04-03</strong></p>  
<!-- raw HTML omitted -->  
</blockquote>  
<p>... (truncated)</p>  
</details>  
<details>  
<summary>Commits</summary>  
<ul>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/ecb2ca118ad3d6c62f2cc90416b58da24db5d18b"><code>ecb2ca1</code></a> update release info</li>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/910dae5be2de2955e968558fadf6e8f74f117a77"><code>910dae5</code></a> fix entities performance &amp; security issues</li>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/fe9a85270122036ae22637167ce38a5f71b73a5f"><code>fe9a852</code></a> update strnum and release detail</li>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/943ef0eb1b2d3284e72dd74f44a042ee9f07026e"><code>943ef0e</code></a> fix: Escape regex char in entity name</li>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/ddcd0acf26ddd682cb0dc15a2bd6aa3b96bb1e69"><code>ddcd0ac</code></a> Escape regex char in entity name</li>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/341b582219b1eb57e4c34ca58881602cba6b8711"><code>341b582</code></a> Add missing exports to fxp commonjs types (<a href="https://redirect.github.com/NaturalIntelligence/fast-xml-parser/issues/782">#782</a>)</li>  
<li>See full diff in <a href="https://github.com/NaturalIntelligence/fast-xml-parser/compare/v5.3.4...v5.3.6">compare view</a></li>  
</ul>  
</details>  
<br />  
  
  
[![Dependabot compatibility score](https://dependabot-badges.githubapp.com/badges/compatibility_score?dependency-name=fast-xml-parser&package-manager=npm_and_yarn&previous-version=5.3.4&new-version=5.3.6)](https://docs.github.com/en/github/managing-security-vulnerabilities/about-dependabot-security-updates#about-compatibility-scores)  
  
Dependabot will resolve any conflicts with this PR as long as you don't alter it yourself. You can also trigger a rebase manually by commenting `@dependabot rebase`.  
  
[//]: # (dependabot-automerge-start)  
[//]: # (dependabot-automerge-end)  
  
---  
  
<details>  
<summary>Dependabot commands and options</summary>  
<br />  
  
You can trigger Dependabot actions by commenting on this PR:  
- `@dependabot rebase` will rebase this PR  
- `@dependabot recreate` will recreate this PR, overwriting any edits that have been made to it  
- `@dependabot show <dependency name> ignore conditions` will show all of the ignore conditions of the specified dependency  
- `@dependabot ignore this major version` will close this PR and stop Dependabot creating any more for this major version (unless you reopen the PR or upgrade to it yourself)  
- `@dependabot ignore this minor version` will close this PR and stop Dependabot creating any more for this minor version (unless you reopen the PR or upgrade to it yourself)  
- `@dependabot ignore this dependency` will close this PR and stop Dependabot creating any more for this dependency (unless you reopen the PR or upgrade to it yourself)  
You can disable automated security fix PRs for this repo from the [Security Alerts page](https://github.com/boostorg/msm/network/alerts).  
  
</details>

---
