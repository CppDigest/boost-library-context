# #86 - Test failure with `back11` in test `OrthogonalDeferredEuml.cpp` [Open]

> Username: chandryan  
> Created at: 2025-03-09 15:50:58 UTC  
> Updated at: 2025-03-09 15:50:58 UTC  
> Url: https://github.com/boostorg/msm/issues/86  

After having added test cases to cover all existing backends in #84 I identified a test failure in `OrthogonalDeferredEuml.cpp` when utilizing `back11`.  
  
I suspect the error lies within the backend and is not related to my refactoring, because the other backend variants for this test and all other tests in general work fine.  
  
The test output is as follows:  
  
```  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(326): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": AllOk should be active  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(328): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": ErrorMode exit not called correctly  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(330): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": AllOk entry not called correctly  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(333): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": Playing should be active  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(335): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": Stopped exit not called correctly  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(336): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": Playing entry not called correctly  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(339): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": AllOk should be active  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(341): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": ErrorTerminate should be active  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(343): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": AllOk exit not called correctly  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(345): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": ErrorTerminate entry not called correctly  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(349): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": ErrorTerminate should be active  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(352): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": Playing should be active  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(358): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": ErrorTerminate should be active  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(359): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": Playing should be active  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(365): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": ErrorTerminate should be active  
/home/xubuntu/ProgrammingVsCode/msm/test/OrthogonalDeferredEuml.cpp(366): error: in "orthogonal_deferred_euml_test<(anonymous namespace)__hierarchical_state_machine<boost__msm__back11__state_machine_ void>>": Playing should be active  
```
