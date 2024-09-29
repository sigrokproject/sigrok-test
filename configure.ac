##
## This file is part of the sigrok-test project.
##
## Copyright (C) 2010 Bert Vermeulen <bert@biot.com>
## Copyright (C) 2014 Uwe Hermann <uwe@hermann-uwe.de>
##
## This program is free software: you can redistribute it and/or modify
## it under the terms of the GNU General Public License as published by
## the Free Software Foundation, either version 3 of the License, or
## (at your option) any later version.
##
## This program is distributed in the hope that it will be useful,
## but WITHOUT ANY WARRANTY; without even the implied warranty of
## MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
## GNU General Public License for more details.
##
## You should have received a copy of the GNU General Public License
## along with this program.  If not, see <http://www.gnu.org/licenses/>.
##

# We require at least autoconf 2.63 (AC_INIT format changed there).
AC_PREREQ([2.63])

AC_INIT([sigrok-test], [0.1.0], [sigrok-devel@lists.sourceforge.net],
	[sigrok-test], [http://www.sigrok.org])
AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_AUX_DIR([autostuff])
AC_CONFIG_HEADERS([config.h])

# We require at least automake 1.11 (needed for 'silent rules').
AM_INIT_AUTOMAKE([1.11 -Wall -Werror no-define subdir-objects check-news])
AM_SILENT_RULES([yes])

AC_CANONICAL_HOST

# Checks for programs.
AC_PROG_CC

# Required for per-target flags or subdir-objects with C sources.
AM_PROG_CC_C_O

# Get compiler versions.
SR_PROG_VERSION([$CC], [st_cc_version])

############################
##  Package dependencies  ##
############################

# Initialize pkg-config.
# We require at least 0.22, as "Requires.private" behaviour changed there.
PKG_PROG_PKG_CONFIG([0.22])

# Keep track of all checked modules so we can list them at the end.
ST_PKGLIBS=
SR_PKG_CHECK_SUMMARY([st_pkglibs_summary])

# Python 3 is always needed.
# Starting with Python 3.8 we need to check for "python-3.8-embed"
# first, since usually only that variant will add "-lpython3.8".
# https://docs.python.org/3/whatsnew/3.8.html#debug-build-uses-the-same-abi-as-release-build
SR_PKG_CHECK([python3], [ST_PKGLIBS],
	[python-3.12-embed], [python-3.11-embed], [python-3.10-embed],
	[python-3.9-embed], [python-3.8-embed], [python3-embed],
	[python-3.8 >= 3.8], [python-3.7 >= 3.7], [python-3.6 >= 3.6],
	[python-3.5 >= 3.5], [python-3.4 >= 3.4], [python-3.3 >= 3.3],
	[python-3.2 >= 3.2], [python3 >= 3.2])
AS_IF([test "x$sr_have_python3" = xno],
	[AC_MSG_ERROR([Cannot find Python 3 development headers.])])

######################
##  Feature checks  ##
######################

AC_LANG([C])

# Enable the C99 standard if possible.
ST_EXTRA_CFLAGS=
SR_CHECK_COMPILE_FLAGS([ST_EXTRA_CFLAGS], [C99],
	[-std=c99 -c99 -AC99 -qlanglvl=extc99])

SR_ARG_ENABLE_WARNINGS([ST_WFLAGS],
	[-Wall], [-Wall -Wextra -Wmissing-prototypes])

# Check host characteristics.
AC_SYS_LARGEFILE

# Checks for header files.
AC_CHECK_HEADER([sys/resource.h],,
	[AC_MSG_ERROR([Missing required header sys/resource.h])])

##############################
##  Finalize configuration  ##
##############################

# Retrieve the compile and link flags for all modules combined.
# Also, bail out at this point if any module dependency is not met.
# Check availability of library version dependent features.
PKG_CHECK_MODULES([SIGROK_TEST],
	[glib-2.0 >= 2.24.0 libsigrok >= 0.5.0 libsigrokdecode >= 0.5.0 $ST_PKGLIBS])
AC_LANG([C])
st_save_cflags=$CFLAGS
st_save_libs=$LIBS
CFLAGS="$SIGROK_TEST_CFLAGS $CFLAGS"
LIBS="$SIGROK_TEST_LIBS $LIBS"
AC_CHECK_FUNCS([srd_session_send_eof])
CFLAGS=$st_save_cflags
LIBS=$st_save_libs

# Allow the user to override the location of the protocol decoders.
AC_ARG_WITH([decodersdir],
	[AS_HELP_STRING([--with-decodersdir=DIR],
			[specify location of protcol decoders [default=auto]])],
	[st_decodersdir=$withval], [st_decodersdir=auto])

AS_IF([test "x$st_decodersdir" = xauto], [
	st_decodersdir=`$PKG_CONFIG --variable=decodersdir libsigrokdecode 2>&AS_MESSAGE_LOG_FD`
	AS_IF([test "[$]?" != 0 || test -z "$st_decodersdir"],
		[st_decodersdir='$(datadir)/libsigrokdecode/decoders'])
])
AC_SUBST([DECODERS_DIR], [$st_decodersdir])

st_glib_version=`$PKG_CONFIG --modversion glib-2.0 2>&AS_MESSAGE_LOG_FD`
st_libsigrok_version=`$PKG_CONFIG --modversion libsigrok 2>&AS_MESSAGE_LOG_FD`
st_libsigrokdecode_version=`$PKG_CONFIG --modversion libsigrokdecode 2>&AS_MESSAGE_LOG_FD`

AC_CONFIG_FILES([Makefile])
AC_OUTPUT

cat >&AS_MESSAGE_FD <<_EOF

sigrok-test configuration summary:
 - Package version................. $PACKAGE_VERSION
 - Prefix.......................... $prefix
 - Decoders directory.............. $st_decodersdir
 - Building on..................... $build
 - Building for.................... $host

Compile configuration:
 - C compiler...................... $CC
 - C compiler version.............. $st_cc_version
 - C compiler flags................ $CFLAGS
 - Additional C compiler flags..... $ST_EXTRA_CFLAGS
 - C compiler warnings............. $ST_WFLAGS
 - Linker flags.................... $LDFLAGS

Detected libraries (required):
 - glib-2.0 >= 2.24.0.............. $st_glib_version
 - libsigrok >= 0.5.0.............. $st_libsigrok_version
 - libsigrokdecode >= 0.5.0........ $st_libsigrokdecode_version
$st_pkglibs_summary
_EOF
