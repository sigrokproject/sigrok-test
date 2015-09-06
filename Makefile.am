##
## This file is part of the sigrok-test project.
##
## Copyright (C) 2014 Uwe Hermann <uwe@hermann-uwe.de>
##
## This program is free software; you can redistribute it and/or modify
## it under the terms of the GNU General Public License as published by
## the Free Software Foundation; either version 2 of the License, or
## (at your option) any later version.
##
## This program is distributed in the hope that it will be useful,
## but WITHOUT ANY WARRANTY; without even the implied warranty of
## MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
## GNU General Public License for more details.
##
## You should have received a copy of the GNU General Public License
## along with this program; if not, write to the Free Software
## Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301 USA
##

ACLOCAL_AMFLAGS = -I m4
GNUMAKEFLAGS = --no-print-directory

AM_CPPFLAGS = -D_POSIX_C_SOURCE=200112L -DDECODERS_DIR='"$(DECODERS_DIR)"'
AM_CFLAGS = $(ST_EXTRA_CFLAGS) $(ST_WFLAGS) $(SIGROK_TEST_CFLAGS)

noinst_PROGRAMS = decoder/runtc

decoder_runtc_SOURCES = decoder/runtc.c
decoder_runtc_LDADD = $(SIGROK_TEST_LIBS)

MAINTAINERCLEANFILES = ChangeLog

.PHONY: ChangeLog
ChangeLog:
	git --git-dir '$(top_srcdir)/.git' log >$@ || touch $@

