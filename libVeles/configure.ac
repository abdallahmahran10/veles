#  This file is a part of SEAPT, Samsung Extended Autotools Project Template

#  Copyright 2012,2013 Samsung R&D Institute Russia
#  All rights reserved.
#
#  Redistribution and use in source and binary forms, with or without
#  modification, are permitted provided that the following conditions are met:
#
#  1. Redistributions of source code must retain the above copyright notice, this
#     list of conditions and the following disclaimer.
#  2. Redistributions in binary form must reproduce the above copyright notice,
#     this list of conditions and the following disclaimer in the documentation
#     and/or other materials provided with the distribution.
#
#  THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
#  ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
#  WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
#  DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR
#  ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
#  (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
#  LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND
#  ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
#  (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
#  SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

AC_INIT(Veles, 1.0)
m4_include([common.ac])
AC_SUBST(INTERFACE_VERSION, [0])
AC_SUBST(REVISION_NUMBER, [$(cd $srcdir && git rev-list HEAD --count)])
AC_SUBST(AGE_NUMBER, [0])

AS_IF([test "x$enable_static" != "xno" -a "x$enable_shared" != "xno" ], [
    AC_MSG_ERROR([Building both shared and static libraries is not supported (use --disable-shared or --disable-static)])
])

AC_CONFIG_SUBDIRS([simd])
SIMD_LIBS="\$(top_builddir)/simd/src/libSimd.la"
SIMD_CFLAGS="-I\$(top_srcdir)/simd/inc"
AC_SUBST([SIMD_CFLAGS])
AC_SUBST([SIMD_LIBS])

# Check whether to use the built-in libarchive
AC_ARG_WITH([built-in-libarchive],
    AS_HELP_STRING([--with-built-in-libarchive], [use bundled libarchive]), [
    LIBARCHIVE_LIBS="\$(top_builddir)/libarchive/libarchive.la"
    LIBARCHIVE_CFLAGS="-I\$(top_srcdir)/libarchive/libarchive"
    LIBARCHIVE_DIR=libarchive
    AC_CONFIG_SUBDIRS([libarchive])
    ZLIB_DIR=zlib
    ZLIB_LIBS="\$(top_builddir)/zlib/libz.a"
    AC_SUBST([ZLIB_LIBS])
    echo "$AM_CFLAGS $CFLAGS $AM_CPPFLAGS $CPPFLAGS -fvisibility=default" > zlib.cflags
    sed -i -e 's/-I$@<:@^ @:>@*//g' -e 's/--coverage//g' zlib.cflags
    AC_CONFIG_COMMANDS([zlib],
                       [ln -sf $ac_top_srcdir/zlib zlib && \
                       cd zlib && \
                       git reset --hard && \
                       CC=$CC CFLAGS="$(cat $ac_abs_top_builddir/zlib.cflags) -fPIC -Wno-implicit-function-declaration" \
                       ./configure --static --const && \
                       cd ..])
], [
    # Check for installed libarchive
    PKG_CHECK_MODULES([LIBARCHIVE], [libarchive >= 3.0])
    PKG_CHECK_MODULES([ZLIB], [zlib >= 1.2.8])
])
AC_SUBST([LIBARCHIVE_DIR])
AC_SUBST([ZLIB_DIR])

AM_CPPFLAGS="$AM_CPPFLAGS -I\$(top_srcdir)/rapidjson/include"

# Check whether to use nice Eina logging
AC_ARG_ENABLE([eina-logging],
    AS_HELP_STRING([--disable-eina-logging], [do not use Eina as the logging backend])
)
AC_ARG_WITH([built-in-eina-logging],
AS_HELP_STRING([--with-built-in-eina-logging], [use bundled eina_log module]), [
    AS_IF([test "x$enable_eina_logging" != "xno"], [
    	EINA_CFLAGS="-DEINA_STRIPPED -I\$(top_srcdir)/eina"
    	EINA_LIBS="\$(top_builddir)/eina/libEina.la"
    	EINA_DIR=eina    	
	])
], [
    AS_IF([test "x$enable_eina_logging" != "xno"], [
        PKG_CHECK_MODULES([EINA], [eina >= 1.0])      
    ])
])
AS_IF([test "x$enable_eina_logging" != "xno"], [
	AM_CPPFLAGS="$AM_CPPFLAGS -DEINA $EINA_CFLAGS"
])
AC_SUBST([EINA_DIR])

AC_CONFIG_COMMANDS([Creating docs directory in build], 
    [mkdir -p docs])
    
AC_CONFIG_LINKS([docs/neural-network.jpg:docs/neural-network.jpg])

AM_COND_IF([TESTS], [
    AC_CONFIG_LINKS([tests/workflow_files/mnist.tar.gz:tests/workflow_files/mnist.tar.gz
                     tests/workflow_files/mnist.zip:tests/workflow_files/mnist.zip
                     tests/workflow_files/contents.json:tests/workflow_files/contents.json])    
])

# Link with Makefile.am in additional source directories
AC_CONFIG_FILES(eina/Makefile)

AC_OUTPUT

AC_DEFUN([VELES_PRINT_STATUS], [
echo
echo -e "${COLOR_WHITE}$PACKAGE_NAME options:${COLOR_RESET}"
echo "  built_in_libarchive.....: $(color_yes_no ${with_built_in_libarchive:-no})"
echo "  eina_logging............: $(color_yes_no ${enable_eina_logging:-yes})"
echo "  built_in_eina_logging...: $(color_yes_no ${with_built_in_eina_logging:-no})"
echo
])

COMMON_PRINT_STATUS
VELES_PRINT_STATUS
