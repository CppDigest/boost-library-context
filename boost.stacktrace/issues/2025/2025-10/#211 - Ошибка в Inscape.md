# #211 - Ошибка в Inscape [Open]

> Username: popovalex2511011703-web  
> Created at: 2025-10-02 20:32:32 UTC  
> Updated at: 2025-11-08 11:41:32 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/211  

<pre>  
 0# boost::stacktrace::basic_stacktrace<std::allocator<boost::stacktrace::frame> >::init(unsigned long long, unsigned long long) in libinkscape_base  
 1# Inkscape::Application::crash_handler(int) in libinkscape_base  
 2# 0x00007FF72CAF2422 in inkscape  
 3# _C_specific_handler in ntdll  
 4# _chkstk in ntdll  
 5# _RtlWow64GetCurrentCpuArea in ntdll  
 6# _KiUserExceptionDispatcher in ntdll  
 7# _gtk_im_context_reset in libgtk_3_0  
 8# _gtk_im_context_simple_add_compose_file in libgtk_3_0  
 9# _g_closure_invoke in libgobject_2_0_0  
10# _g_param_spec_variant in libgobject_2_0_0  
11# _g_param_spec_variant in libgobject_2_0_0  
12# _g_signal_emit in libgobject_2_0_0  
13# _g_cclosure_marshal_BOOLEAN__BOXED_BOXEDv in libgobject_2_0_0  
14# _g_object_notify_by_pspec in libgobject_2_0_0  
15# _gdk_event_free in libgdk_3_0  
16# _gdk_win32_drag_context_get_type in libgdk_3_0  
17# _g_clear_list in libglib_2_0_0  
18# _g_get_monotonic_time in libglib_2_0_0  
19# _g_main_context_iteration in libglib_2_0_0  
20# _g_application_run in libgio_2_0_0  
21# 0x00007FF72CAF4052 in inkscape  
22# 0x00007FF72CAF1319 in inkscape  
23# 0x00007FF72CAF1406 in inkscape  
24# _BaseThreadInitThunk in KERNEL32  
25# _RtlUserThreadStart in ntdll  
</pre>  
<details><summary>System info</summary>  
Inkscape 1.4.2 (f4327f4, 2025-05-13)  
  
                      Compile  (Run)  
    GLib version:     2.84.0  
    GTK version:      3.24.49 (3.24.49)  
    glibmm version:   2.66.7  
    gtkmm version:    3.24.9  
    libxml2 version:  2.12.10  
    libxslt version:  1.1.43  
    Cairo version:    1.18.4 (1.18.4)  
    Pango version:    1.56.3 (1.56.3)  
    HarfBuzz version: 10.4.0 (10.4.0)  
  
    OS version:       Windows 11 24H2  
</details>

---

## Comment 1

> Username: apolukhin  
> Created at: 2025-11-08 11:41:32 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/211#issuecomment-3506472868  

This is not enough to reproduce the issue. Please provide a source code of a minimal reproducer
