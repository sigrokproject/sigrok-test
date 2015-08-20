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

# We require at least automake 1.11 (needed for 'silent rules').
AM_INIT_AUTOMAKE([1.11 -Wall -Werror subdir-objects check-news])
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])

# Enable more compiler warnings.
AM_CFLAGS="$AM_CFLAGS -Wall -Wextra -Wmissing-prototypes"

# Checks for programs.
AC_PROG_CC

# Required for per-target flags or subdir-objects with C sources.
AM_PROG_CC_C_O

# Initialize pkg-config.
# We require at least 0.22, as "Requires.private" behaviour changed there.
PKG_PROG_PKG_CONFIG([0.22])

AC_ARG_WITH(decodersdir,
	AC_HELP_STRING([--with-decodersdir=path],
	[specify decodersdir [default=DATAROOTDIR/libsigrokdecode/decoders]]),
	[DECODERS_DIR=$withval], [DECODERS_DIR=$datadir/libsigrokdecode/decoders])

# Checks for libraries.

# libglib-2.0 is always needed.
AM_PATH_GLIB_2_0([2.24.0],
	[AM_CFLAGS="$AM_CFLAGS $GLIB_CFLAGS"; LIBS="$LIBS $GLIB_LIBS"])

# Python 3 is always needed.
# Note: We need to try a few different variants, since some systems have a
# python3.pc file, others have a python-3.3.pc file, and so on.
# See also: http://sigrok.org/wiki/Libsigrokdecode/Python
CPPFLAGS_PYTHON=""
LDFLAGS_PYTHON=""
pyver="none"
PKG_CHECK_MODULES([python3], [python3 >= 3.2],
	[pyver=`$PKG_CONFIG --modversion python3`;
	CPPFLAGS_PYTHON="$CPPFLAGS_PYTHON $python3_CFLAGS";
	LDFLAGS_PYTHON="$LDFLAGS_PYTHON $python3_LIBS"],
[PKG_CHECK_MODULES([python34], [python-3.4 >= 3.4],
	[pyver=`$PKG_CONFIG --modversion python-3.4`;
	CPPFLAGS_PYTHON="$CPPFLAGS_PYTHON $python34_CFLAGS";
	LDFLAGS_PYTHON="$LDFLAGS_PYTHON $python34_LIBS"],
[PKG_CHECK_MODULES([python33], [python-3.3 >= 3.3],
	[pyver=`$PKG_CONFIG --modversion python-3.3`;
	CPPFLAGS_PYTHON="$CPPFLAGS_PYTHON $python33_CFLAGS";
	LDFLAGS_PYTHON="$LDFLAGS_PYTHON $python33_LIBS"],
[PKG_CHECK_MODULES([python32], [python-3.2 >= 3.2],
	[pyver=`$PKG_CONFIG --modversion python-3.2`;
	CPPFLAGS_PYTHON="$CPPFLAGS_PYTHON $python32_CFLAGS";
	LDFLAGS_PYTHON="$LDFLAGS_PYTHON $python32_LIBS"],
)])])])
AM_CFLAGS="$AM_CFLAGS $CPPFLAGS_PYTHON"
AM_LDFLAGS="$AM_LDFLAGS $LDFLAGS_PYTHON"

# libsigrok is always needed.
PKG_CHECK_MODULES([libsigrok], [libsigrok >= 0.3.0],
	[AM_CFLAGS="$AM_CFLAGS $libsigrok_CFLAGS";
	LIBS="$LIBS $libsigrok_LIBS"])

# libsigrokdecode is always needed.
PKG_CHECK_MODULES([libsigrokdecode], [libsigrokdecode >= 0.4.0],
	[AM_CFLAGS="$AM_CFLAGS $libsigrokdecode_CFLAGS";
	LIBS="$LIBS $libsigrokdecode_LIBS"])

# Checks for header files.
AC_CHECK_HEADER([sys/resource.h])

AC_SUBST(DECODERS_DIR)
AC_SUBST(MAKEFLAGS, '--no-print-directory')
AC_SUBST(AM_LIBTOOLFLAGS, '--silent')

AC_SUBST(AM_CFLAGS)
AC_SUBST(AM_LDFLAGS)

AC_CONFIG_FILES([Makefile])

AC_OUTPUT

echo
echo "sigrok-test configuration summary:"
echo
echo "  - Package version (major.minor.micro): 0.1.0"
echo "  - Prefix: $prefix"
echo
echo "Detected libraries:"
echo

if test "x$pyver" = "xnone"; then
	echo "  - (REQUIRED) python >= 3.2: no"
else
	echo "  - (REQUIRED) python >= 3.2: yes ($pyver)"
fi

# Note: This only works for libs with pkg-config integration.
for lib in "glib-2.0 >= 2.24.0" "libsigrok >= 0.3.0" "libsigrokdecode >= 0.4.0"; do
	optional="REQUIRED"
	if `$PKG_CONFIG --exists $lib`; then
		ver=`$PKG_CONFIG --modversion $lib`
		answer="yes ($ver)"
	else
		answer="no"
	fi
	echo "  - ($optional) $lib: $answer"
done
