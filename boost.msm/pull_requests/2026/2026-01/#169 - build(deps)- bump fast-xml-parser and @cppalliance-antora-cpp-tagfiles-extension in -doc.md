# #169 build(deps): bump fast-xml-parser and @cppalliance/antora-cpp-tagfiles-extension in /doc [Merged]

> Username: dependabot[bot]  
> Created at: 2026-01-30 21:28:51 UTC  
> Updated at: 2026-01-31 14:54:17 UTC  
> Merged at: 2026-01-31 14:54:09 UTC  
> Closed at: 2026-01-31 14:54:09 UTC  
> Url: https://github.com/boostorg/msm/pull/169  

Bumps [fast-xml-parser](https://github.com/NaturalIntelligence/fast-xml-parser) to 5.3.4 and updates ancestor dependency [@cppalliance/antora-cpp-tagfiles-extension](https://github.com/cppalliance/antora-cpp-tagfiles-extension). These dependencies need to be updated together.  
  
Updates `fast-xml-parser` from 4.4.1 to 5.3.4  
<details>  
<summary>Release notes</summary>  
<p><em>Sourced from <a href="https://github.com/NaturalIntelligence/fast-xml-parser/releases">fast-xml-parser's releases</a>.</em></p>  
<blockquote>  
<h2>fix: handle HTML numeric and hex entities when out of range</h2>  
<p>No release notes provided.</p>  
<h2>bug fix and performance improvements</h2>  
<ul>  
<li>fix <a href="https://redirect.github.com/NaturalIntelligence/fast-xml-parser/issues/775">#775</a>: transformTagName with allowBooleanAttributes adds an unnecessary attribute</li>  
<li>Performance improvement for stopNodes (By <a href="https://github.com/macieklamberski">Maciek Lamberski</a>)</li>  
</ul>  
<h2>Replace <code>Buffer</code> with <code>Uint8Array</code></h2>  
<ul>  
<li>Launched Separate CLI module</li>  
<li>Replace <code>Buffer</code> with <code>Uint8Array</code></li>  
</ul>  
<h2>Support EMPTY and ANY with ELEMENT in DOCTYPE</h2>  
<p><strong>Full Changelog</strong>: <a href="https://github.com/NaturalIntelligence/fast-xml-parser/compare/v5.2.4...v5.2.4">https://github.com/NaturalIntelligence/fast-xml-parser/compare/v5.2.4...v5.2.4</a></p>  
<h2>upgrade to ESM module and fixing value parsing issues</h2>  
<ul>  
<li>Support ESM modules</li>  
<li>fix value parsing issues</li>  
<li>a feature to access tag location is added (metadata)</li>  
<li>fix to read DOCTYPE correctly</li>  
</ul>  
<p><strong>Full Changelog</strong>: <a href="https://github.com/NaturalIntelligence/fast-xml-parser/blob/master/CHANGELOG.md">https://github.com/NaturalIntelligence/fast-xml-parser/blob/master/CHANGELOG.md</a></p>  
<h2>Summary update on all the previous releases from v4.2.4</h2>  
<ul>  
<li>Multiple minor fixes provided in the validator and parser</li>  
<li>v6 is added for experimental use.</li>  
<li>ignoreAttributes support function, and array of string or regex</li>  
<li>Add support for parsing HTML numeric entities</li>  
<li>v5 of the application is ESM module now. However, JS is also supported</li>  
</ul>  
<p><strong>Note</strong>: Release section in not updated frequently. Please check <a href="https://github.com/NaturalIntelligence/fast-xml-parser/blob/master/CHANGELOG.md">CHANGELOG</a> or <a href="https://github.com/NaturalIntelligence/fast-xml-parser/tags">Tags</a> for latest release information.</p>  
</blockquote>  
</details>  
<details>  
<summary>Changelog</summary>  
<p><em>Sourced from <a href="https://github.com/NaturalIntelligence/fast-xml-parser/blob/master/CHANGELOG.md">fast-xml-parser's changelog</a>.</em></p>  
<blockquote>  
<p><!-- raw HTML omitted -->Note: If you find missing information about particular minor version, that version must have been changed without any functional change in this library.<!-- raw HTML omitted --></p>  
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
<ul>  
<li>feat: support metadata on nodes (<a href="https://redirect.github.com/NaturalIntelligence/fast-xml-parser/issues/593">#593</a>) (By <a href="https://github.com/srl295">Steven R. Loomis</a>)</li>  
</ul>  
<p><strong>5.1.0 / 2025-04-02</strong></p>  
<ul>  
<li>feat: declare package as side-effect free (<a href="https://redirect.github.com/NaturalIntelligence/fast-xml-parser/issues/738">#738</a>) (By <a href="https://github.com/tbouffard">Thomas Bouffard</a>)</li>  
<li>fix cjs build mode</li>  
<li>fix builder return type to string</li>  
<li></li>  
</ul>  
<p><strong>5.0.9 / 2025-03-14</strong></p>  
<ul>  
<li>fix: support numeric entities with values over 0xFFFF (<a href="https://redirect.github.com/NaturalIntelligence/fast-xml-parser/issues/726">#726</a>) (By <a href="https://github.com/mcdurdin">Marc Durdin</a>)</li>  
<li>fix: update strnum to fix parsing 0 if skiplike option is used</li>  
</ul>  
<p><strong>5.0.8 / 2025-02-27</strong></p>  
<!-- raw HTML omitted -->  
</blockquote>  
<p>... (truncated)</p>  
</details>  
<details>  
<summary>Commits</summary>  
<ul>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/753e770f69d0f30c95b0287f7b8c57e4a93bbc12"><code>753e770</code></a> update release details</li>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/4e387f61c4a5cef792f6a2f42467013290bf95dc"><code>4e387f6</code></a> handle html entities when out of range</li>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/088b47a660b13003644e4399c48f0d4593fb4e59"><code>088b47a</code></a> correct typo (<a href="https://redirect.github.com/NaturalIntelligence/fast-xml-parser/issues/780">#780</a>)</li>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/f335cbf80462b821ec9ee2b1e864c0b762515397"><code>f335cbf</code></a> update publish detail</li>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/fcedd3104b9eec41fa5da3fa6260accfb1e10794"><code>fcedd31</code></a> fix <a href="https://redirect.github.com/NaturalIntelligence/fast-xml-parser/issues/775">#775</a>: transformTagName with allowBooleanAttributes adds an unnecessary at...</li>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/54d22627adafd4603286ed36b7d18a8506f3ed31"><code>54d2262</code></a> Fix captureMetaData casing (<a href="https://redirect.github.com/NaturalIntelligence/fast-xml-parser/issues/773">#773</a>)</li>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/e0769f20cfbb67037e2db691401c71f9e101c547"><code>e0769f2</code></a> update publish detail</li>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/1a810ad58ed9b4b0714553fd8c92759e51efb1fe"><code>1a810ad</code></a> fix import for v6</li>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/ab148aba3ae73c3c85dda956e286633418ede383"><code>ab148ab</code></a> update pcakge detail to publish</li>  
<li><a href="https://github.com/NaturalIntelligence/fast-xml-parser/commit/81513d5eb96f80bb9f48e5b9ce21d69d2c8433be"><code>81513d5</code></a> perf: Use Set for stopNodes lookup (O(1)) instead of array iteration (O(n)) (...</li>  
<li>Additional commits viewable in <a href="https://github.com/NaturalIntelligence/fast-xml-parser/compare/v4.4.1...v5.3.4">compare view</a></li>  
</ul>  
</details>  
<br />  
  
Updates `@cppalliance/antora-cpp-tagfiles-extension` from 0.0.4 to 0.1.0  
<details>  
<summary>Release notes</summary>  
<p><em>Sourced from <a href="https://github.com/cppalliance/antora-cpp-tagfiles-extension/releases"><code>@​cppalliance/antora-cpp-tagfiles-extension</code>'s releases</a>.</em></p>  
<blockquote>  
<h2>v0.1.0</h2>  
<h2>🚀 Features</h2>  
<p>New features and additions</p>  
<ul>  
<li>✨ Antora-cpp-reference-extension coordination. 98eba40 (thanks <a href="https://github.com/alandefreitas"><code>@​alandefreitas</code></a>)</li>  
</ul>  
<h2>🏗️ Chores</h2>  
<p>Routine tasks, maintenance, and housekeeping</p>  
<ul>  
<li>Version is 0.1.0. 453f01b (thanks <a href="https://github.com/alandefreitas"><code>@​alandefreitas</code></a>)</li>  
</ul>  
<blockquote>  
<p>Parent release: <a href="https://github.com/cppalliance/antora-cpp-tagfiles-extension/releases/tag/v0.0.5">v0.0.5</a> e23ceb7</p>  
</blockquote>  
<h2>v0.0.5</h2>  
<h2>🚦 Continuous Integration</h2>  
<p>Changes related to continuous integration</p>  
<ul>  
<li>Npm publish continues on error. 25d5448</li>  
<li>Exercise release workflow. e23ceb7</li>  
</ul>  
<h2>🏗️ Chores</h2>  
<p>Routine tasks, maintenance, and housekeeping</p>  
<ul>  
<li>Update packages. daac961</li>  
<li>Version is 0.0.5. c20c29b</li>  
</ul>  
<blockquote>  
<p>Parent release: <a href="https://github.com/cppalliance/antora-cpp-tagfiles-extension/releases/tag/v0.0.4">v0.0.4</a> ef26557</p>  
</blockquote>  
</blockquote>  
</details>  
<details>  
<summary>Commits</summary>  
<ul>  
<li><a href="https://github.com/cppalliance/antora-cpp-tagfiles-extension/commit/453f01b0c2b33a14a93c3242b48e41adcd0a5535"><code>453f01b</code></a> chore: version is 0.1.0</li>  
<li><a href="https://github.com/cppalliance/antora-cpp-tagfiles-extension/commit/98eba4003fd19cc02a2a5e52316b0109ca457d6c"><code>98eba40</code></a> feat: antora-cpp-reference-extension coordination</li>  
<li><a href="https://github.com/cppalliance/antora-cpp-tagfiles-extension/commit/e23ceb75cbcdba38bb5046539d0d9e568102b3f8"><code>e23ceb7</code></a> ci: exercise release workflow</li>  
<li><a href="https://github.com/cppalliance/antora-cpp-tagfiles-extension/commit/c20c29bc21840cdee5b4bf649814e53980ef1811"><code>c20c29b</code></a> chore: version is 0.0.5</li>  
<li><a href="https://github.com/cppalliance/antora-cpp-tagfiles-extension/commit/daac9613978f9a76ba90cb58b377b1053dc49c4f"><code>daac961</code></a> chore: update packages</li>  
<li><a href="https://github.com/cppalliance/antora-cpp-tagfiles-extension/commit/25d5448e0eeb2eea729ab3a3a9f060a8ecbf9ac9"><code>25d5448</code></a> ci: npm publish continues on error</li>  
<li>See full diff in <a href="https://github.com/cppalliance/antora-cpp-tagfiles-extension/compare/v0.0.4...v0.1.0">compare view</a></li>  
</ul>  
</details>  
<br />  
  
  
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
- `@dependabot merge` will merge this PR after your CI passes on it  
- `@dependabot squash and merge` will squash and merge this PR after your CI passes on it  
- `@dependabot cancel merge` will cancel a previously requested merge and block automerging  
- `@dependabot reopen` will reopen this PR if it is closed  
- `@dependabot close` will close this PR and stop Dependabot recreating it. You can achieve the same result by closing it manually  
- `@dependabot show <dependency name> ignore conditions` will show all of the ignore conditions of the specified dependency  
- `@dependabot ignore this major version` will close this PR and stop Dependabot creating any more for this major version (unless you reopen the PR or upgrade to it yourself)  
- `@dependabot ignore this minor version` will close this PR and stop Dependabot creating any more for this minor version (unless you reopen the PR or upgrade to it yourself)  
- `@dependabot ignore this dependency` will close this PR and stop Dependabot creating any more for this dependency (unless you reopen the PR or upgrade to it yourself)  
You can disable automated security fix PRs for this repo from the [Security Alerts page](https://github.com/boostorg/msm/network/alerts).  
  
</details>

---
