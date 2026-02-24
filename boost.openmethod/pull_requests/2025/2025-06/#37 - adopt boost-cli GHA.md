# #37 adopt boost-cli GHA [Closed]

> Username: jll63  
> Created at: 2025-06-22 19:26:14 UTC  
> Updated at: 2025-07-07 20:37:30 UTC  
> Closed at: 2025-07-07 20:37:30 UTC  
> Url: https://github.com/boostorg/openmethod/pull/37  



---

## Review 1 [Commented]

> Username: copilot-pull-request-reviewer[bot]  
> Created_at: 2025-06-30 21:11:10 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/openmethod/pull/37#pullrequestreview-2972747501  

## Pull Request Overview  
  
This PR aims to adopt the boost-cli GitHub Actions configuration while aligning test framework and build configuration updates. Key changes include:  
- Replacing the merged Boost.Test header with the modular <boost/test/unit_test.hpp> header across test files.  
- Updating module names and test target names to better reflect the tested components.  
- Introducing new GitHub Actions workflows (ci.yml) and removing the legacy main.yml.  
  
### Reviewed Changes  
  
Copilot reviewed 27 out of 27 changed files in this pull request and generated no comments.  
  
<details>  
<summary>Show a summary per file</summary>  
  
| File                                                 | Description                                                                                                     |  
|------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|  
| test/test_virtual_ptr_value_semantics.cpp            | Updated Boost.Test header include.                                                                              |  
| test/test_virtual_ptr_dispatch.cpp                   | Updated Boost.Test header include.                                                                              |  
| test/test_unique_virtual_ptr_value_semantics.cpp     | Updated Boost.Test header include.                                                                              |  
| test/test_static_list.cpp                            | Changed test module name to "static_list" and updated include order.                                            |  
| test/test_shared_virtual_ptr_value_semantics.cpp      | Updated Boost.Test header include.                                                                              |  
| test/test_runtime_errors.cpp                         | Changed BOOST_TEST literal comparisons to use unsigned constants.                                               |  
| test/test_policies.cpp                               | Updated Boost.Test header include and test module name.                                                         |  
| test/test_pointer_to_method.cpp                      | Updated Boost.Test header include.                                                                              |  
| test/test_n2216.cpp                                  | Updated Boost.Test header include.                                                                              |  
| test/test_member_method.cpp                          | Updated Boost.Test header include.                                                                              |  
| test/test_intrusive.cpp                              | Corrected test module name from "instrusive" to "intrusive".                                                    |  
| test/test_dispatch.cpp                               | Updated Boost.Test header include.                                                                              |  
| test/test_custom_rtti.cpp                            | Changed error return from nullptr to type_id(0) and adjusted type name mapping accordingly.                      |  
| test/test_core.cpp                                   | Updated Boost.Test header include.                                                                              |  
| test/test_compiler.cpp                               | Updated Boost.Test header include.                                                                              |  
| test/compile_fail_not_polymorphic_virtual_ptr.cpp    | New compile-fail test added for non-polymorphic virtual pointer usage.                                            |  
| test/cmake_subdir_test/main.cpp                      | Added a comprehensive usage example covering multiple override scenarios.                                       |  
| test/cmake_subdir_test/CMakeLists.txt                | Updated CMake configuration for subdirectory tests.                                                             |  
| test/cmake_install_test/main.cpp                     | Added installation test entry point similar to the subdirectory test example.                                   |  
| test/cmake_install_test/CMakeLists.txt               | Updated CMake config for installation tests.                                                                    |  
| test/Jamfile                                         | Updated Jamfile rules with unit_test_framework alias and compile-fail targets.                                    |  
| test/CMakeLists.txt                                  | Updated test target linking and added dependencies to ensure unit test framework is linked.                       |  
| meta/libraries.json                                  | New file containing library metadata.                                                                           |  
| README.md                                            | Minor badge/documentation updates.                                                                              |  
| CMakeLists.txt                                       | Updated top-level CMake configuration to use modern CMake practices, include proper target compile features, etc.    |  
| .github/workflows/main.yml                           | Removed legacy workflow file.                                                                                     |  
| .github/workflows/ci.yml                             | Added a comprehensive GitHub Actions CI workflow covering multiple platforms and configurations.                 |  
</details>  
  
  
  
<details>  
<summary>Comments suppressed due to low confidence (2)</summary>  
  
**test/test_custom_rtti.cpp:451**  
* Replacing 'return nullptr;' with 'return type_id(0);' changes the signaling of an unknown type. Please add a comment or documentation clarifying that a value of 0 in type_id explicitly represents an unknown or non-polymorphic type to ensure consumers handle it appropriately.  
```  
                return type_id(0);  
```  
  
</details>

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2025-06-30 21:13:04 UTC  
> Url: https://github.com/boostorg/openmethod/pull/37#issuecomment-3020765648  

## Welcome to [Codecov](https://codecov.io?utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=Jean-Louis+Leroy) :tada:  
  
Once you merge this PR into your default branch, you're all set! Codecov will compare coverage reports and display results in all future pull requests.  
  
Thanks for integrating Codecov - We've got you covered :open_umbrella:

---
