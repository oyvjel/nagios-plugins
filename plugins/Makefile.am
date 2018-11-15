## Process this file with automake to produce Makefile.in

NP_VERSION = 1.0

AM_CFLAGS = -DNP_VERSION='"$(NP_VERSION)"'

VPATH = $(top_srcdir) $(top_srcdir)/lib $(top_srcdir)/plugins

# $(top_srcdir)/plugins/t

AM_CPPFLAGS = -I.. -I$(top_srcdir)/lib -I$(top_srcdir)/gl -I$(top_srcdir)/intl
#	@SSLINCLUDE@

localedir = $(datadir)/locale
# gettext docs say to use AM_CPPFLAGS, but per module_CPPFLAGS override this
DEFS = -DLOCALEDIR=\"$(localedir)\" @DEFS@
# gettext docs say to use LDADD, but per module_LDADD override
LIBS = @LTLIBINTL@ @LIBS@



MATHLIBS = @MATHLIBS@

# This is not portable. Run ". tools/devmode" to get development compile flags
#AM_CFLAGS = -Wall

libexec_PROGRAMS = hello \
	ssltest \
	check_tcp \
	check_http

#	check_ping \
#	urlize @EXTRAS@

check_tcp_programs = check_ftp check_imap check_nntp check_pop \
        check_clamd \
	check_udp @check_tcp_ssl@

EXTRA_PROGRAMS = 

#check_mysql check_radius check_pgsql check_snmp check_hpjd \
#	check_swap check_fping check_ldap check_game check_dig \
#	check_nagios check_by_ssh check_dns check_nt check_ide_smart	\
#	check_procs check_mysql_query check_apt check_dbi check_uptime

# EXTRA_DIST = t tests

PLUGINHDRS = common.h

noinst_LIBRARIES = libnpcommon.a

libnpcommon_a_SOURCES = utils.c netutils.c sslutils.c \
	utils.h netutils.h common.h
#	popen.c runcmd.c \
#	popen.h runcmd.h

BASEOBJS = libnpcommon.a ../lib/libnagiosplug.a ../gl/libgnu.a

NETOBJS = $(BASEOBJS) $(EXTRA_NETOBLS)
NETLIBS = $(NETOBJS) $(SOCKETLIBS)
SSLOBJS = $(NETLIBS) $(SSLLIBS)

##############################################################################
# the actual targets

LDADD  = $(WINLIBS)

ssltest_LDADD = $(SSLOBJS)
check_http_LDADD = $(SSLOBJS)
check_tcp_LDADD = $(SSLOBJS)
#check_by_ssh_LDADD = $(NETLIBS)
#check_ide_smart_LDADD = $(BASEOBJS)
#negate_LDADD = $(BASEOBJS)
#urlize_LDADD = $(BASEOBJS)

#if !HAVE_UTMPX
#check_users_LDADD += popen.o
#endif

##############################################################################
# secondary dependencies

all-local: $(check_tcp_programs)

$(check_tcp_programs): check_tcp$(EXEEXT)
	rm -f $@$(EXEEXT)
	ln -s check_tcp$(EXEEXT) $@$(EXEEXT)

install-exec-hook:
	cd $(DESTDIR)$(libexecdir) && \
	for i in $(check_tcp_programs) ; do rm -f $$i; ln -s check_tcp $$i ; done ;\
	if [ -x check_ldap ] ; then rm -f check_ldaps ; ln -s check_ldap check_ldaps ; fi

clean-local:
	rm -f $(check_tcp_programs)
	rm -f NP-VERSION-FILE

uninstall-local:
	cd $(DESTDIR)$(libexecdir) && rm -f $(check_tcp_programs)
