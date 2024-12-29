# This file is part of the 'Simple Sokoban' project.
#
# MIT LICENSE
#
# Copyright (C) 2014-2024 Mateusz Viste
#
# Permission is hereby granted, free of charge, to any person obtaining a
# copy of this software and associated documentation files (the "Software"),
# to deal in the Software without restriction, including without limitation
# the rights to use, copy, modify, merge, publish, distribute, sublicense,
# and/or sell copies of the Software, and to permit persons to whom the
# Software is furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
# FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER
# DEALINGS IN THE SOFTWARE.

AUTOMAKE_OPTIONS	=	foreign

SUBDIRS			=	skins

WINDRES			=	@WINDRES@


bin_PROGRAMS		=	simplesok

BUILT_SOURCES		=	data.h					\
				data.c

nodist_simplesok_SOURCES=	data.c

simplesok_SOURCES	=	simplesok.c				\
				crc32.c					\
				gra.c					\
				gz.c					\
				save.c					\
				skin.c					\
				sok_core.c				\
				crc32.h					\
				gra.h					\
				gz.h					\
				net.h					\
				save.h					\
				skin.h					\
				sok_core.h

simplesok_CFLAGS	=	@SDL2_CFLAGS@ @ZLIB_CFLAGS@		\
				-O3 -Wall -Wextra -std=gnu89 -pedantic  \
				-Wno-long-long -Wformat-security
simplesok_LDADD		=	@SDL2_LIBS@ @ZLIB_LIBS@

if DISABLE_LIBCURL
simplesok_SOURCES	+=	net-socket.c
else
simplesok_SOURCES	+=	net-curl.c
simplesok_CFLAGS	+=	@LIBCURL_CFLAGS@
simplesok_LDADD		+=	@LIBCURL_LIBS@
endif

if NATIVE_WINDOWS
.rc.o:
	$(WINDRES) -i $< --output-format coff -o $@

simplesok_CFLAGS	+=	-mwindows
simplesok_SOURCES	+=	resources.rc
simplesok_LDFLAGS	=	-static-libgcc -Dmain=SDL_main
simplesok_LDADD		+=	-lmingw32 -luser32 -lgdi32
if DISABLE_LIBCURL
simplesok_LDADD		+=	-lws2_32
endif
endif


dist_man_MANS		=	simplesok.6

dist_doc_DATA		=	history.txt				\
				simplesok.txt				\
				xsb_format.txt

EXTRA_DIST		=	assets					\
				pkgbuild				\
				simplesok.desktop			\
				simplesok.ico				\
				simplesok.png				\
				simplesok.svg				\
				file2c

DISTCLEANFILES		=	$(BUILT_SOURCES)


data.c:		data.h
data.h:		file2c assets/img/*.bmp.gz skins/yoshi.png	\
		assets/font/*.bmp.gz assets/levels/*.xsb.gz assets/icon.bmp.gz
	echo '/* This file is part of the simplesok project. */' > data.c
	echo '#include <stddef.h> /* size_t */' >> data.c
	echo '#include "data.h"' >> data.c
	for x in assets/img/*.bmp.gz; do ./file2c "$${x}"; done >> data.c
	./file2c skins/yoshi.png >> data.c
	for x in assets/levels/*.xsb.gz; do ./file2c "$${x}"; done >> data.c
	for x in assets/font/*.bmp.gz; do ./file2c "$${x}"; done >> data.c
	./file2c assets/icon.bmp.gz >> data.c
	sed -e '/^unsigned char.*\[\]/!d;s/^\([^]]*\]\).*/extern \1;/' \
		< data.c > data.h
	sed -e '/_len/!d;s/^\(.*_len\).*/extern \1;/' < data.c >> data.h
	touch data.c

skins/%.gz:	skins/%
	$(MAKE) -C skins
