# #146 - Demangle fails for long symbol names [Closed]

> Username: nilsvu  
> Created at: 2023-05-25 16:35:11 UTC  
> Updated at: 2023-05-25 18:12:49 UTC  
> Closed at: 2023-05-25 18:12:49 UTC  
> Url: https://github.com/boostorg/core/issues/146  

`boost::core::demangle` fails if the mangled name is too long. I am hitting this issue for symbols with lots of templates, like this one:  
  
```  
_ZZN8Parallel6detail39create_initialization_item_from_optionsI13MetavariablesILm1EEN6domain4Tags6DomainILm1EEEJNS_10OptionTags12ResourceInfoIS3_EEN8elliptic10OptionTags10BackgroundINSB_13analytic_data10BackgroundEEENSC_12InitialGuessINSE_12InitialGuessEEENS4_10OptionTags29RadiallyCompressedCoordinatesENSK_13DomainCreatorILm1EEEN7logging10OptionTags9VerbosityIN12SolvePoisson10OptionTags17LinearSolverGroupEEEN11Convergence10OptionTags8CriteriaIST_EEN9observers10OptionTags17ReductionFileNameENS10_14VolumeFileNameEN7Options4Tags11InputSourceENSW_10IterationsINSS_14MultigridGroupEEEN12LinearSolver9multigrid10OptionTags9MaxLevelsIS17_EENS1B_16OutputVolumeDataIS17_EENS16_INSS_20SchwarzSmootherGroupEEENS19_7Schwarz10OptionTags10MaxOverlapIS1G_EEN7Actions18RandomizeVariablesIN4Tags9VariablesIN7brigand4listIJN7Poisson4Tags5FieldEEEEEENS3_21RandomizeInitialGuessEE25RandomParametersOptionTagENSB_2dg10OptionTags7MassiveENS21_16PenaltyParameterEN10OptionTags17EventsAndTriggersENSQ_IS17_EENS1B_18EnablePreSmoothingIS17_EENSQ_IS1G_EENS1J_24ObservePerCoreReductionsIS1G_EENS1B_27EnablePostSmoothingAtBottomIS17_EENS1J_15SubdomainSolverISt10unique_ptrINS19_6Serial12LinearSolverINS1R_IJNS2G_10Registrars5GmresINS1I_28ElementCenteredSubdomainDataILm1ENS1R_IJNS19_4Tags8ResidualINS2L_14PreconditionedINS2L_7OperandIS1U_EEEEEEEEEEEEEN12Registration9RegistrarINS2G_15ExplicitInverseEJEEEEEEEESt14default_deleteIS30_EES1G_EEELDn0EEENT0_4typeERKN6tuples11TaggedTupleIJDpT1_EEEENKUlDpRKT_E_clIJS2F_I13DomainCreatorILm1EES31_IS3L_EEEEEDaS3H_  
```  
  
Some notes from https://github.com/sxs-collaboration/spectre/pull/3907:  
  
- Apparently there are some internal size limits with stack-allocated C-style strings in libiberty from GNU binutils for some reason.  
- There's [a check that the length of an array is less than the recursion limit](https://sourceware.org/git/?p=binutils-gdb.git;a=blob;f=libiberty/cp-demangle.c;hb=4311246bb1f94ca323b0cf74e917d2b8d3a87565#l6454). The default recursion limit is 2048, and the array has twice the length of the mangled name, so names longer than 1024 fail. Here's the original binutils issue: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=87335  
- There's a flag to c++filt to disable the check, but it may not be exposed in the c interface.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-05-25 18:12:49 UTC  
> Url: https://github.com/boostorg/core/issues/146#issuecomment-1563313762  

If the ABI library fails to demangle a name (due to internal limits or other reasons) then there's nothing we can do. `boost::core::demangle` does not demangle the symbol name itself, it's just a wrapper around the ABI library.
