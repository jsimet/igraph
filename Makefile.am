ACLOCAL_AMFLAGS = -I m4

SUBDIRS=src tests
DOC_FILES = $(top_srcdir)/doc/Makefile.in \
	$(top_srcdir)/doc/igraph.3

pkgconfigdir = @libdir@/pkgconfig
pkgconfig_DATA = igraph.pc

EXTRA_DIST = igraph.pc IGRAPH_VERSION $(top_srcdir)/include/* $(DOC_FILES) examples \
	$(wildcard $(top_srcdir)/optional/glpk/*.{inc,c,h})      \
	$(wildcard $(top_srcdir)/optional/glpk/{README,COPYING}) \
	$(wildcard $(top_srcdir)/optional/glpk/amd/*.{c,h})      \
	$(wildcard $(top_srcdir)/optional/glpk/amd/{README,COPYING}) \
	$(wildcard $(top_srcdir)/optional/glpk/colamd/*.{c,h}) \
	$(wildcard $(top_srcdir)/optional/glpk/colamd/{README,COPYING}) \
	tests/testsuite \
	ACKNOWLEDGEMENTS.md CHANGELOG.md

tests/testsuite:
	cd tests && make testsuite

install-exec-hook:
	if test -f $(top_builddir)/src/.libs/cygigraph-0.dll ; \
		then cp $(top_builddir)/src/.libs/cygigraph-0.dll \
		$(DESTDIR)$(libdir) ; fi

install-info:
	if test -f doc/igraph.info; then d="doc"; \
         else d=$(srcdir); fi; \
        $(INSTALL_DATA) $$d/igraph.info $(infodir); \
        if $(SHELL) -c 'install-info --version' \
           >/dev/null 2>&1; then \
          install-info --infodir=$(infodir) $$d/igraph.info; \
        else true; fi

dist-hook:
	rm -rf `find $(distdir)/examples -type d -name .arch-ids`

MAINTAINERCLEANFILES = Makefile.in

## to make sure make deb will generate Debian packages
.PHONY: framework msvc parsersources

framework: all
	rm -rf $(top_builddir)/igraph.framework
	mkdir -p $(top_builddir)/igraph.framework/Versions/$(VERSION)/Headers
	mkdir -p $(top_builddir)/igraph.framework/Versions/$(VERSION)/Resources
	ln -s $(VERSION) $(top_builddir)/igraph.framework/Versions/Current
	ln -s Versions/Current/Headers $(top_builddir)/igraph.framework/Headers
	ln -s Versions/Current/Resources $(top_builddir)/igraph.framework/Resources
	cp $(top_srcdir)/include/* $(top_builddir)/igraph.framework/Headers/
	if [ $(top_builddir) != $(top_srcdir) ]; then cp $(top_builddir)/include/* $(top_builddir)/igraph.framework/Headers/; fi
	cp $(top_builddir)/src/.libs/libigraph.dylib $(top_builddir)/igraph.framework/Versions/Current/igraph
	ln -s Versions/Current/igraph $(top_builddir)/igraph.framework/igraph
	cp $(top_builddir)/igraph_Info.plist $(top_builddir)/igraph.framework/Versions/Current/Resources/Info.plist

parsersources:
	cd src; make parsersources

## in case you are thinking about replacing the ugly find;echo hack below with
## xargs -r, don't. This is not supported on macOS (no -r option in xargs
## there)
msvc: parsersources
	rm -rf $(top_builddir)/igraph-$(VERSION)-msvc
	mkdir $(top_builddir)/igraph-$(VERSION)-msvc
	mkdir $(top_builddir)/igraph-$(VERSION)-msvc/include
	cp -r $(top_srcdir)/src $(top_builddir)/igraph-$(VERSION)-msvc/
	cp -r $(top_srcdir)/include/*.h $(top_builddir)/igraph-$(VERSION)-msvc/include
	if [ "x$(top_srcdir)" != "x$(top_builddir)" ]; then cp -r $(top_builddir)/include $(top_builddir)/src $(top_builddir)/igraph-$(VERSION)-msvc; fi
	cp -r $(top_srcdir)/msvc/include $(top_builddir)/igraph-$(VERSION)-msvc/winclude
	cp -r $(top_srcdir)/msvc/src/f2c/arith.h $(top_builddir)/igraph-$(VERSION)-msvc/src/f2c
	( find $(top_builddir)/igraph-$(VERSION)-msvc/src -type d \( -name .deps -o -name .libs \); echo ':' ) | xargs rm -rf
	( find $(top_builddir)/igraph-$(VERSION)-msvc/src -type f \( -name '*.o' -o -name '*.lo' -o -name '*.la' \); echo ':' ) | xargs rm -f
	( find $(top_builddir)/igraph-$(VERSION)-msvc/src -type f \( -name 'Makefile*' -o -name '.dirstamp' \); echo ':' ) | xargs rm -f
	rm -rf $(top_builddir)/igraph-$(VERSION)-msvc/src/f2c/arith
	rm -rf $(top_builddir)/igraph-$(VERSION)-msvc/src/config.h
	rm -rf $(top_builddir)/igraph-$(VERSION)-msvc/src/*~
	rm -rf $(top_builddir)/igraph-$(VERSION)-msvc/include/*~
	mkdir $(top_builddir)/igraph-$(VERSION)-msvc/winlib
	$(top_srcdir)/tools/create-msvc-projectfile.py		\
		$(top_builddir)/igraph-$(VERSION)-msvc 	\
		$(top_srcdir)/msvc/igraph.vcproj        \
		$(top_builddir)/src/Makefile
	cp $(top_srcdir)/msvc/igraph.sln $(top_builddir)/igraph-$(VERSION)-msvc
	cp -r $(top_srcdir)/msvc/igraphtest $(top_builddir)/igraph-$(VERSION)-msvc/test
	rm -rf igraph-$(VERSION)-msvc.zip
	zip -q -r igraph-$(VERSION)-msvc.zip igraph-$(VERSION)-msvc

CLEANFILES=
