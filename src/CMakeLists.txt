# Copyright 2019 Free Software Foundation, Inc.
#
#  This file is part of the GNU MP Library.
#
#  The GNU MP Library is free software; you can redistribute it and/or modify
#  it under the terms of either:
#
#    * the GNU Lesser General Public License as published by the Free
#      Software Foundation; either version 3 of the License, or (at your
#      option) any later version.
#
#  or
#
#    * the GNU General Public License as published by the Free Software
#      Foundation; either version 2 of the License, or (at your option) any
#      later version.
#
#  or both in parallel, as here.
#
#  The GNU MP Library is distributed in the hope that it will be useful, but
#  WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY
#  or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License
#  for more details.
#
#  You should have received copies of the GNU General Public License and the
#  GNU Lesser General Public License along with the GNU MP Library.  If not,
#  see https://www.gnu.org/licenses/.

set(LIBMPFR_SOURCES
  mpfr.h mpf2mpfr.h mpfr-cvers.h mpfr-gmp.h
  mpfr-impl.h mpfr-intmax.h mpfr-longlong.h mpfr-sassert.h mpfr-thread.h
  exceptions.c extract.c uceil_exp2.c
  uceil_log2.c ufloor_log2.c add.c add1.c add_ui.c agm.c clear.c cmp.c
  cmp_abs.c cmp_si.c cmp_ui.c comparisons.c div_2exp.c div_2si.c
  div_2ui.c div.c div_ui.c dump.c eq.c exp10.c exp2.c exp3.c exp.c
  frac.c frexp.c get_d.c get_exp.c get_str.c init.c inp_str.c isinteger.c
  isinf.c isnan.c isnum.c const_log2.c log.c modf.c mul_2exp.c mul_2si.c
  mul_2ui.c mul.c mul_ui.c neg.c next.c out_str.c printf.c vasprintf.c
  const_pi.c pow.c pow_si.c pow_ui.c print_raw.c print_rnd_mode.c
  reldiff.c round_prec.c set.c setmax.c setmin.c set_d.c set_dfl_prec.c
  set_exp.c set_rnd.c set_f.c set_prc_raw.c set_prec.c set_q.c set_si.c
  set_str.c set_str_raw.c set_ui.c set_z.c sqrt.c sqrt_ui.c sub.c sub1.c
  sub_ui.c rint.c ui_div.c ui_sub.c urandom.c urandomb.c get_z_exp.c
  swap.c factorial.c cosh.c sinh.c tanh.c sinh_cosh.c acosh.c asinh.c
  atanh.c atan.c cmp2.c exp_2.c asin.c const_euler.c cos.c sin.c tan.c
  fma.c fms.c hypot.c log1p.c expm1.c log2.c log10.c ui_pow.c
  ui_pow_ui.c minmax.c dim.c signbit.c copysign.c setsign.c gmp_op.c
  init2.c acos.c sin_cos.c set_nan.c set_inf.c set_zero.c powerof2.c
  gamma.c set_ld.c get_ld.c cbrt.c volatile.c fits_s.h fits_sshort.c
  fits_sint.c fits_slong.c fits_u.h fits_ushort.c fits_uint.c
  fits_ulong.c fits_uintmax.c fits_intmax.c get_si.c get_ui.c zeta.c
  cmp_d.c erf.c inits.c inits2.c clears.c sgn.c check.c sub1sp.c
  version.c mpn_exp.c mpfr-gmp.c mp_clz_tab.c sum.c add1sp.c
  free_cache.c si_op.c cmp_ld.c set_ui_2exp.c set_si_2exp.c set_uj.c
  set_sj.c get_sj.c get_uj.c get_z.c iszero.c cache.c sqr.c
  int_ceil_log2.c isqrt.c strtofr.c pow_z.c logging.c mulders.c get_f.c
  round_p.c erfc.c atan2.c subnormal.c const_catalan.c root.c
  gen_inverse.h sec.c csc.c cot.c eint.c sech.c csch.c coth.c
  round_near_x.c constant.c abort_prec_max.c stack_interface.c lngamma.c
  zeta_ui.c set_d64.c get_d64.c jn.c yn.c rem1.c get_patches.c add_d.c
  sub_d.c d_sub.c mul_d.c div_d.c d_div.c li2.c rec_sqrt.c min_prec.c
  buildopt.c digamma.c bernoulli.c isregular.c set_flt.c get_flt.c
  scale2.c set_z_exp.c ai.c gammaonethird.c ieee_floats.h
  grandom.c fpif.c set_float128.c get_float128.c rndna.c nrandom.c
  random_deviate.h random_deviate.c erandom.c mpfr-mini-gmp.c
  mpfr-mini-gmp.h fmma.c log_ui.c gamma_inc.c ubf.c invert_limb.h
  invsqrt_limb.h beta.c odd_p.c get_q.c pool.c
)

add_library(libmpfr SHARED ${LIBMPFR_SOURCES})
target_compile_definitions(libmpfr PRIVATE __MPFR_WITHIN_MPFR)
target_link_libraries(libmpfr GMP::libgmp)
set_target_properties(libmpfr
  PROPERTIES
  OUTPUT_NAME libmpfr$<$<CONFIG:Debug>:d>-${MPFR_LT_CURRENT}
  VERSION ${MPFR_VERSION}
  SOVERSION ${MPFR_LT_CURRENT})

include(GNUInstallDirs)

# Install libmpfr.
install(
  TARGETS libmpfr EXPORT libmpfr-export
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

# Install libmpfr header file.
install(
  FILES mpfr.h
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
  COMPONENT Development)

# Install libmpfr PDB files.
if(MSVC)
  install(
    FILES $<TARGET_PDB_FILE:libmpfr>
    DESTINATION ${CMAKE_INSTALL_BINDIR}
    COMPONENT Development
    OPTIONAL)
endif()

include(CMakePackageConfigHelpers)

# Generate the config files of MPFR.
write_basic_package_version_file(
  "${CMAKE_BINARY_DIR}/cmake/MPFRConfigVersion.cmake"
  VERSION ${MPFR_VERSION}
  COMPATIBILITY SameMajorVersion)
export(
  EXPORT libmpfr-export
  NAMESPACE MPFR::
  FILE "${CMAKE_BINARY_DIR}/cmake/libmpfr.cmake")

# Install the config files of MPFR.
install(
  FILES
    ${CMAKE_SOURCE_DIR}/cmake/MPFRConfig.cmake
    ${CMAKE_BINARY_DIR}/cmake/MPFRConfigVersion.cmake
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/mpfr
  COMPONENT Development)
install(EXPORT libmpfr-export
  FILE libmpfr.cmake
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/mpfr
  NAMESPACE MPFR::
  COMPONENT Development)
