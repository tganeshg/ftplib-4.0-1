#
# This makefile contains modifications submitted by Richard Braakman
# (dark@xs4all.nl) for the shared library generation.
#

# By default, ftplib uses PASV.  If you need it to use  PORT
# instead, uncomment the next line
#DEFINES = -DFTPLIB_DEFMODE=FTPLIB_PORT
CC=arm-openwrt-linux-gcc

SONAME = 4
SOVERSION = $(SONAME).0

TARGETS = qftp libftp.so libftp.a qftp.static
OBJECTS = qftp.o ftplib.o
SOURCES = qftp.c ftplib.c

CFLAGS = -Wall $(DEBUG) -I. $(INCLUDES) $(DEFINES) -Wno-unused-variable -D_FILE_OFFSET_BITS=64 -D__unix__
LDFLAGS = -L.
DEPFLAGS =

all : $(TARGETS)

clean :
	rm -f $(OBJECTS) core *.bak
	rm -rf unshared 

clobber : clean
	rm -f $(TARGETS) .depend
	rm -f libftp.so.*

install : all
	install qftp /usr/local/bin
	install -m 644 libftp.so.$(SOVERSION) /usr/local/lib
	install -m 644 ftplib.h /usr/local/include
	(cd /usr/local/lib && \
	 ln -sf libftp.so.$(SOVERSION) libftp.so.$(SONAME) && \
	 ln -sf libftp.so.$(SONAME) libftp.so)
	-(cd /usr/local/bin && \
	  for f in ftpdir ftpget ftplist ftprm ftpsend; \
	  do ln -s qftp $$f; done)

depend :
	$(CC) $(CFLAGS) -M $(SOURCES) > .depend

# build without -fPIC
unshared/ftplib.o: ftplib.c ftplib.h
	test -d unshared || mkdir unshared
	$(CC) -c $(CFLAGS) -D_REENTRANT $< -o $@

static : libftp.a qftp.static

qftp.static : qftp.o libftp.a
	$(CC) -o $@ $< libftp.a

ftplib.o: ftplib.c ftplib.h
	$(CC) -c $(CFLAGS) -fPIC -D_REENTRANT $< -o $@

libftp.a: unshared/ftplib.o
	ar -rcs $@ $<

libftp.so.$(SOVERSION): ftplib.o
	$(CC) -shared -Wl,-soname,libftp.so.$(SONAME) -lc -o $@ $<

libftp.so: libftp.so.$(SOVERSION)
	ln -sf $< libftp.so.$(SONAME)
	ln -sf $< $@

qftp : qftp.o libftp.so ftplib.h
	$(CC) $(LDFLAGS) -o $@ $< -lftp

ifeq (.depend,$(wildcard .depend))
include .depend
endif
