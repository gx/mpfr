
include_directories(
  ${CMAKE_CURRENT_SOURCE_DIR}/../src
  ${CMAKE_CURRENT_BINARY_DIR}/../src)

add_library(
  libfrtests
  mpfr-test.h memory.c rnd_mode.c tests.c cmp_str.c random2.c)
target_link_libraries(libfrtests libmpfr)

if (NOT "${CMAKE_CURRENT_BINARY_DIR}" STREQUAL "${CMAKE_CURRENT_SOURCE_DIR}")
  file(COPY inp_str.dat tmul.dat tfpif_r1.dat tfpif_r2.dat
    DESTINATION ${CMAKE_CURRENT_BINARY_DIR})
endif()

set(tests
  tversion tabort_prec_max tassert tabort_defalloc1
  tabort_defalloc2
  #talloc # removed due to requiring C99
  tinternals tinits tisqrt tsgn tcheck
  tisnan texceptions tset_exp tset mpf_compat mpfr_compat reuse
  tabs tacos tacosh tadd tadd1sp tadd_d tadd_ui tagm tai
  talloc-cache tasin tasinh tatan tatanh taway tbeta tbuildopt
  tcan_round tcbrt tcmp tcmp2 tcmp_d tcmp_ld tcmp_ui tcmpabs
  tcomparisons tconst_catalan tconst_euler tconst_log2 tconst_pi
  tcopysign tcos tcosh tcot tcoth tcsc tcsch td_div td_sub tdigamma
  tdim tdiv tdiv_d tdiv_ui teint teq terandom terandom_chisq terf
  texp texp10 texp2 texpm1 tfactorial tfits tfma tfmma tfmod tfms
  tfpif tfprintf tfrac tfrexp tgamma tgamma_inc tget_d tget_d_2exp
  tget_f tget_flt tget_ld_2exp tget_q
  #tget_set_d64 # removed due to requiring GCC extension (decimal floats)
  tget_sj
  tget_str tget_z tgmpop tgrandom thyperbolic thypot tinp_str
  tj0 tj1 tjn tl2b tlgamma tli2 tlngamma tlog tlog10 tlog1p tlog2
  tlog_ui tmin_prec tminmax tmodf tmul tmul_2exp tmul_d tmul_ui
  tnext tnrandom tnrandom_chisq tout_str toutimpl tpow tpow3
  tpow_all tpow_z tprintf trandom trandom_deviate trec_sqrt
  tremquo trint trndna troot trootn_ui tround_prec tsec tsech
  tset_d tset_f
  #tset_float128 # removed due to requiring GCC extension (__float128)
  tset_ld tset_q tset_si tset_sj
  tset_str tset_z tset_z_exp tsi_op tsin tsin_cos tsinh tsinh_cosh
  tsprintf tsqr tsqrt tsqrt_ui tstckintc tstdint tstrtofr
  tsub tsub1sp tsub_d tsub_ui tsubnormal tsum tswap ttan ttanh
  ttrunc tui_div tui_pow tui_sub turandom tvalist ty0 ty1 tyn
  tzeta tzeta_ui)

foreach(t ${tests})
  add_executable(${t} ${t}.c)
  target_link_libraries(${t} libmpfr libfrtests)
  add_test(${t} ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${t})
  set_target_properties(${t} PROPERTIES FOLDER Tests)
endforeach()

if(HAVE_STDINT_H)
  set_source_files_properties(tstdint.c PROPERTIES COMPILE_DEFINITIONS HAVE_STDINT_H)
endif()

if(HAVE_STDARG)
  set_source_files_properties(tvalist.c PROPERTIES COMPILE_DEFINITIONS HAVE_STDARG)
endif()
