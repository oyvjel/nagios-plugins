dnl Process this file with autoconf to produce a configure script.
# AC_PREREQ(2.59)
AC_INIT(npwin, 1.0, oyvind.jelstad@embriq.no)#

AC_CONFIG_SRCDIR(plugins/check_tcp.c)
AC_CONFIG_MACRO_DIR([gl/m4])
AC_CONFIG_LIBOBJ_DIR([gl])

AC_CONFIG_FILES([
  Makefile
  gl/Makefile
  lib/Makefile
  plugins/Makefile
       ])

# AC_CONFIG_AUX_DIR(build-aux)
AM_INIT_AUTOMAKE([foreign])

# AM_MAINTAINER_MODE([enable])

AC_CONFIG_HEADERS([config.h])

AC_CANONICAL_HOST

DEFAULT_PATH="/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin"

RELEASE=1
AC_SUBST(RELEASE)

AC_PREFIX_DEFAULT(/usr/local/nagios)

AC_PROG_CC
gl_EARLY

AC_PROG_GCC_TRADITIONAL
# AC_PROG_LIBTOOL
AM_PROG_CC_C_O


###AM_PROG_AR

AC_FUNC_ERROR_AT_LINE
AC_SYS_LARGEFILE

AC_LIBOBJ([getaddrinfo])
AC_LIBSOURCES([getaddrinfo.c])

## EXTRAS=
## EXTRAS_ROOT=
dnl PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/etc:/usr/local/bin:/usr/local/sbin:$PATH

LDFLAGS="$LDFLAGS -L."


dnl Check if version file is present
AM_CONDITIONAL([RELEASE_PRESENT], [test -f $srcdir/release])

# Also read in the version from it
if test -f $srcdir/release; then
	NP_RELEASE="$(<release)"
else
	NP_RELEASE="$PACKAGE_VERSION"
fi
AC_SUBST(NP_RELEASE)

############## ##  ifdef([AC_FUNC_STRTOD],[AC_FUNC_STRTOD],[AM_FUNC_STRTOD])

dnl Checks for programs.
AC_PATH_PROG(PYTHON,python)
#AX_WITH_PYTHON([2.6], [missing])
AC_PATH_PROG(SH,sh)
AC_PATH_PROG(PERL,perl)
AC_PATH_PROG(LIBGNUTLS_CONFIG,libgnutls-config)
AC_PATH_PROG(HOSTNAME,hostname)
AC_PATH_PROG(BASENAME,basename)



dnl openssl/gnutls
AC_ARG_WITH(openssl,
            AC_HELP_STRING([--with-openssl=DIR],
                           [path to openssl installation]),)

AC_ARG_WITH(gnutls,
            ACX_HELP_STRING([--with-gnutls=PATH],
                            [path to gnutls installation root]),)

dnl you can only have one or the other
if test ! "$with_openssl" = "" && test ! "$with_openssl" = "no"; then
	with_gnutls="no"
fi
if test ! "$with_gnutls" = "" && test ! "$with_gnutls" = "no"; then
	with_openssl="no"
fi

dnl list of possible dirs to try to autodetect openssl
dnl if $dir/include exists, we consider it found
dnl the order should allow locally installed versions to override distros ones
OPENSSL_DIRS=" /usr /usr/local /usr/slocal /usr/local/openssl /usr/local/ssl \
              /opt /opt/openssl"
#              /opt /opt/openssl `pwd`/openssl "


dnl
dnl Checks for libraries.
dnl

### MINGW libraries:
# AC_CHECK_LIB([ws2_32], [WSAGetLastError])
AC_CHECK_LIB(ws2_32, main)
AC_CHECK_LIB(gdi32, main)
AC_CHECK_LIB(pthread, main)

#AC_CHECK_LIB(ws2_32,main,WINLIBS="-lws2_32 -lpthread")
#AC_SUBST(WINLIBS)

AC_CHECK_LIB(dce,main,SOCKETLIBS="$SOCKETLIBS -ldce")
AC_CHECK_LIB(nsl,main,SOCKETLIBS="$SOCKETLIBS -lnsl")
AC_CHECK_LIB(socket,socket,SOCKETLIBS="$SOCKETLIBS -lsocket")
AC_CHECK_LIB(resolv,main,SOCKETLIBS="$SOCKETLIBS -lresolv")
AC_SUBST(SOCKETLIBS)

dnl
dnl check for math-related functions needing -lm
AC_CHECK_HEADERS(math.h)
AC_CHECK_LIB(m,floor,MATHLIBS="-lm")
AC_CHECK_HEADERS(mp.h)
AC_CHECK_LIB(bsd,pow,MATHLIBS="$MATHLIBS -lbsd")
AC_SUBST(MATHLIBS)

dnl Check for headers used by check_users
AC_CHECK_HEADERS(utmpx.h)
AM_CONDITIONAL([HAVE_UTMPX], [test "$ac_cv_header_utmpx_h" = "yes"])

AC_CHECK_HEADERS(wtsapi32.h, [], [], [#include <windows.h>])
AM_CONDITIONAL([HAVE_WTS32API], [test "$ac_cv_header_wtsapi32_h" = "yes"])

if test "$ac_cv_header_wtsapi32_h" = "yes"; then
  WTSAPI32LIBS="-lwtsapi32"
  AC_SUBST(WTSAPI32LIBS)
fi

AC_CHECK_HEADERS(sys/select.h strings.h)
### AC_CHECK_FUNCS([bcopy])

dnl ------------------------------------------------------
AC_HEADER_TIME()
AC_CHECK_HEADERS(sys/ioctl.h termios.h sys/mman.h sys/socket.h netdb.h netinet/in.h arpa/inet.h sys/time.h time.h sys/wait.h sys/resource.h io.h linux/personality.h stddef.h stdint.h inttypes.h elf.h execinfo.h stdlib.h syscall.h sys/types.h sys/stat.h unistd.h math.h pthread.h assert.h alloca.h malloc.h dlfcn.h)
AC_CHECK_HEADERS(winsock2.h) dnl used with ws2_32.dll under mingw64
dnl winsock2.h should be included before including windows.h
dnl  pthread.h includes windows.h
dnl   therefore winsock2.h should be included before pthread
AC_SEARCH_LIBS(clock_gettime,rt)
dnl winsock2.h should be used with ws2_32.lib; it defines:
dnl accept bind closesocket connect freeaddrinfo getaddrinfo gethostbyaddr
dnl gethostbyname gethostname getnameinfo getpeername getprotobyname
dnl getprotobynumber getservbyname getservbyport getsockname getsockopt htonl htons
dnl inet_addr inet_ntoa inet_ntop inet_pton ioctlsocket listen ntohl ntohs recv
dnl recvfrom select send sendto setsockopt shutdown socket
AC_SEARCH_LIBS(socket,socket ws2_32) dnl ws2_32 is used under mingw64
AC_SEARCH_LIBS(hstrerror,resolv)
AC_SEARCH_LIBS(dlopen,dl)
AC_SEARCH_LIBS(gethostbyname,nsl)
AC_SUBST(HAVE_LIBTBB,no)
AC_SUBST(LIBTBB,)
   AC_LANG(C++)
      AC_CHECK_HEADER(tbb/tbb.h,
            [AC_SEARCH_LIBS(TBB_runtime_interface_version,tbb,
	      LIBTBB=$ac_cv_search_TBB_runtime_interface_version
	      HAVE_LIBTBB=yes)])
      AC_LANG(C)
AC_CHECK_FUNCS([herror error backtrace clock_gettime __environ _environ environ _setmode hstrerror sync getpgrp setpgid fchmod pipe waitpid setrlimit alarm fork sigprocmask kill longjmp siglongjmp sigaction wait4 readlink lstat realpath mkdir link symlink socket accept fcntl personality ioctl])

AC_SUBST(HAVE_PERSONALITY,$ac_cv_func_personality)

dnl AC_FUNC_ACCEPT_ARGTYPES()

AC_DEFINE_UNQUOTED(SOCKLEN_T,[`echo "$ac_cv_func_accept_arg3" | sed 's/\*$//'`],[socket length type used by accept()])

AC_CHECK_DECLS([ADDR_NO_RANDOMIZE],,,[#include <linux/personality.h>])
AC_CHECK_DECLS([herror],,,[
    #ifdef HAVE_STDLIB_H
    #include <stdlib.h>
    #endif
    #include <stdio.h>
    #include <errno.h>
 ])
AC_CHECK_DECLS([__environ,_environ,environ],,,[#include <unistd.h>])
 
if test $host_os = mingw32
then # CPPFLAGS="$CPPFLAGS -D_POSIX" # arrange for SIGPIPE to get defined
      CPPFLAGS="$CPPFLAGS -D__USE_MINGW_ALARM" # for SIGALRM to get defined
      # OJ: https://lists.gnu.org/archive/html/bug-gnulib/2013-03/msg00040.html suggest HAVE_DECL_ALARM and
      # signal (SIGALRM, SIG_DFL);
      # AC_CHECK_DECLS_ONCE([alarm])
dnl      AC_REPLACE_FUNCS ([getaddrinfo])
fi


dnl =============================================================

AC_SUBST(HAVE_SYS_CDEFS_H,0)
AC_CHECK_HEADERS(sys/cdefs.h)

AC_CHECK_FUNCS([index geteuid timegm localtime_r])

dnl Fallback to who(1) if the system doesn't provide an utmpx(5) interface
if test "$ac_cv_header_utmpx_h" = "no" -a "$ac_cv_header_wtsapi32_h" = "no"
then
	AC_PATH_PROG(PATH_TO_WHO,who)

	if [$PATH_TO_WHO -q 2>/dev/null | egrep -i "^# users=[0-9]+$" >/dev/null]
	then
		ac_cv_path_to_who="$PATH_TO_WHO -q"
	else
		ac_cv_path_to_who="$PATH_TO_WHO"
	fi

	AC_DEFINE_UNQUOTED(WHO_COMMAND,"$ac_cv_path_to_who",
		[path and arguments for invoking 'who'])
fi

AC_ARG_WITH([ipv6],
	[AS_HELP_STRING([--with-ipv6], [support IPv6 @<:@default=check@:>@])],
	[], [with_ipv6=check])

dnl Check for AF_INET6 support - unistd.h required for Darwin
if test "$with_ipv6" != "no"; then
	AC_CACHE_CHECK([for IPv6 support], np_cv_sys_ipv6, [
		AC_TRY_COMPILE(
			[#ifdef HAVE_UNISTD_H
			#include <unistd.h>
			#endif
			#include <netinet/in.h>
			#include <sys/socket.h>],
			[struct sockaddr_in6 sin6;
			void *p;

			sin6.sin6_family = AF_INET6;
			sin6.sin6_port = 587;
			p = &sin6.sin6_addr;],
			[np_cv_sys_ipv6=yes],
			[np_cv_sys_ipv6=no])
		])
	if test "$np_cv_sys_ipv6" = "no" -a "$with_ipv6" != "check"; then
		AC_MSG_FAILURE([--with-ipv6 was given, but test for IPv6 support failed])
	fi
	if test "$np_cv_sys_ipv6" = "yes"; then
		AC_DEFINE(USE_IPV6,1,[Enable IPv6 support])
	fi
	with_ipv6="$np_cv_sys_ipv6"
fi



dnl openssl detection/configuration
if ! test x"$with_openssl" = x"no"; then
	dnl Check for OpenSSL location if it wasn't already specified
	if ! test -d "$with_openssl"; then
		for d in $OPENSSL_DIRS; do
				AC_MSG_CHECKING( dir $d)

			if test -x ${d}/bin/openssl || test -x ${d}/sbin/openssl || test -x ${d}/apps/openssl.exe ; then
				with_openssl=$d
				AC_MSG_CHECKING(OK)

			fi
		done
	fi

	_SAVEDCPPFLAGS="$CPPFLAGS"
	_SAVEDLDFLAGS="$LDFLAGS"
	dnl Check for OpenSSL header files
	unset FOUNDINCLUDE
	if test x"$with_openssl" != x"/usr" ; then
		CPPFLAGS="$CPPFLAGS -I$with_openssl/include"
##		LDFLAGS="$LDFLAGS -L$with_openssl/lib"
		LDFLAGS="$LDFLAGS -L$with_openssl"
	fi

	dnl check for openssl in $dir/include/openssl
	AC_CHECK_HEADERS(openssl/ssl.h openssl/x509.h openssl/rsa.h openssl/pem.h openssl/crypto.h openssl/err.h,
	                 SSLINCLUDE="-I$with_openssl/include"
	                 FOUNDINCLUDE=yes,
	                 FOUNDINCLUDE=no)
	dnl else check to see if $dir/include has it
	if test "$FOUNDINCLUDE" = "no"; then
		AC_CHECK_HEADERS(ssl.h x509.h rsa.h pem.h crypto.h err.h,
		                 SSLINCLUDE="-I$with_openssl/include"
		                 FOUNDINCLUDE=yes,
		                 FOUNDINCLUDE=no)
	fi
	AC_SUBST(SSLINCLUDE)
	dnl if we didn't find it, reset CPPFLAGS
	if test "$FOUNDINCLUDE" = "no"; then
		CPPFLAGS="$_SAVEDCPPFLAGS"
		LDFLAGS="$_SAVEDLDFLAGS"
	fi

	dnl Check for crypto lib
	AC_CHECK_LIB(crypt32,main)
	_SAVEDLIBS="$LIBS"
##	LIBS="-L${with_openssl}/lib"
	LIBS="-L./${with_openssl}"
###	LIBS="-L/home/oyvind/nagios-plugins/w2/openssl"
	AC_MSG_CHECKING(LIBS = $LIBS, Saved libs = $_SAVEDLIBS)
###	MINGWSSL="-lws2_32 -lcrypt32" 

	AC_CHECK_LIB(crypto,CRYPTO_new_ex_data,,,$_SAVEDLIBS)
	if test "$ac_cv_lib_crypto_CRYPTO_new_ex_data" = "yes"; then
		dnl Check for SSL lib : Order: -lssl -lcrypto -lws2_32 -lcrypt32
		MINGWSSL="-lcrypto $MINGWSSL" 
		AC_CHECK_LIB(ssl,main, SSLLIBS="-lssl $MINGWSSL",,$MINGWSSL)
	fi
	LIBS="$_SAVEDLIBS"

	dnl test headers and libs to decide whether check_http should use SSL
	if test "$ac_cv_lib_crypto_CRYPTO_new_ex_data" = "yes"; then
		if test "$ac_cv_lib_ssl_main" = "yes"; then
			if test "$FOUNDINCLUDE" = "yes"; then
				FOUNDOPENSSL="yes"
			fi
		fi
	fi
fi


dnl check for gnutls if openssl isn't found (or is disabled)
if test ! "$FOUNDOPENSSL" = "yes" && test ! "$with_gnutls" = "no"; then
	if test ! "$with_gnutls" = ""; then
		CPPFLAGS="$CPPFLAGS -I${with_gnutls}/include"
	elif test ! "$LIBGNUTLS_CONFIG" = ""; then
		CPPFLAGS="$CPPFLAGS -I`$LIBGNUTLS_CONFIG --prefix`"
	fi
	AC_CHECK_HEADERS([gnutls/openssl.h],FOUNDGNUTLS="yes",)
	if test "$FOUNDGNUTLS" = "yes"; then
		AC_CHECK_LIB(gnutls-openssl,main,SSLLIBS="-lgnutls-openssl")
	fi
fi
dnl end check for gnutls

if test "$FOUNDOPENSSL" = "yes" || test "$FOUNDGNUTLS" = "yes"; then
	check_tcp_ssl="check_simap check_spop check_jabber check_nntps check_ssmtp"
	AC_SUBST(check_tcp_ssl)
	AC_SUBST(SSLLIBS)
	AC_DEFINE(HAVE_SSL,1,[Define if SSL libraries are found])
	if test "$FOUNDOPENSSL" = "yes"; then
		AC_DEFINE(USE_OPENSSL,1,[Define if using OpenSSL libraries])
		with_openssl="yes"
		with_gnutls="no"
	else
		AC_DEFINE(USE_GNUTLS,1,[Define if using gnutls libraries])
		with_gnutls="yes"
		with_openssl="no"
	fi
else
	dnl else deliberately disabled or no ssl support available
	AC_MSG_WARN([OpenSSL or GnuTLS libs could not be found or were disabled])
	with_openssl="no"
	with_gnutls="no"
fi

dnl
dnl Checks for header files.
dnl

AC_HEADER_TIME
AC_HEADER_SYS_WAIT
AC_CHECK_HEADERS(signal.h syslog.h uio.h errno.h sys/time.h sys/socket.h sys/un.h sys/poll.h)
AC_CHECK_HEADERS(features.h stdarg.h sys/unistd.h ctype.h)

dnl Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_STRUCT_TM
AC_TYPE_PID_T
AC_TYPE_SIZE_T
AC_TYPE_SIGNAL

AC_CACHE_CHECK([for va_copy],ac_cv_HAVE_VA_COPY,[
AC_TRY_LINK([#include <stdarg.h>
va_list ap1,ap2;], [va_copy(ap1,ap2);],
ac_cv_HAVE_VA_COPY=yes,
ac_cv_HAVE_VA_COPY=no)])
if test x"$ac_cv_HAVE_VA_COPY" = x"yes"; then
    AC_DEFINE(HAVE_VA_COPY,1,[Whether va_copy() is available])
else
    AC_CACHE_CHECK([for __va_copy],ac_cv_HAVE___VA_COPY,[
    AC_TRY_LINK([#include <stdarg.h>
    va_list ap1,ap2;], [__va_copy(ap1,ap2);],
    ac_cv_HAVE___VA_COPY=yes,
    ac_cv_HAVE___VA_COPY=no)])
    if test x"$ac_cv_HAVE___VA_COPY" = x"yes"; then
        AC_DEFINE(HAVE___VA_COPY,1,[Whether __va_copy() is available])
    fi
fi


AC_TRY_COMPILE([#include <sys/time.h>],
               [struct timeval *tv;
                struct timezone *tz;],
               AC_DEFINE(HAVE_STRUCT_TIMEVAL,1,[Define if we have a timeval structure])
               AC_TRY_COMPILE([#include <sys/time.h>],
                              [struct timeval *tv;
                               struct timezone *tz;
                               gettimeofday(tv, tz);],
                              AC_DEFINE(HAVE_GETTIMEOFDAY,1,[Define if gettimeofday is found]),
                              AC_DEFINE(NEED_GETTIMEOFDAY,1,[Define if gettimeofday is needed])))

dnl Checks for library functions.
AC_CHECK_FUNCS(memmove select socket strdup strstr strtol strtoul floor sigaction)
AC_CHECK_FUNCS(poll)

AC_MSG_CHECKING(return type of socket size)
AC_TRY_COMPILE([#include <stdlib.h>
                #include <sys/types.h>
                #include <sys/socket.h>],
               [int a = send(1, (const void *) buffer, (size_t *) 0, (int *) 0);],
               ac_cv_socket_size_type=["size_t"]
                AC_MSG_RESULT(size_t),
               ac_cv_socket_size_type=["int"]
                AC_MSG_RESULT(int))

AC_DEFINE_UNQUOTED(SOCKET_SIZE_TYPE, $ac_cv_socket_size_type ,
	[Define type of socket size])


AC_SUBST(EXTRAS)
AC_SUBST(EXTRAS_ROOT)
AC_SUBST(EXTRA_NETOBJS)
AC_SUBST(DEPLIBS)

AM_GNU_GETTEXT([external], [need-ngettext])
AM_GNU_GETTEXT_VERSION(0.15)

#################################
dnl External libraries - see ACKNOWLEDGEMENTS
gl_INIT

dnl Some helpful common compile errors checked here
if test "$ac_cv_uname_s" = 'SunOS' -a \( "x$ac_cv_prog_ac_ct_AR" = "x" -o "$ac_cv_prog_ac_ct_AR" = 'false' \) ; then
	AC_MSG_ERROR(No ar found for Solaris - is /usr/ccs/bin in PATH?)
fi

#AC_OUTPUT(
#  Makefile
#  lib/Makefile
#  plugins/Makefile
#  lib/tests/Makefile
#  po/Makefile.in
#)
#  plugins-python/Makefile


dnl the ones below that are commented out need to be cleaned up
dnl in the configure code above to use with_foo instead of ac_cv_foo
dnl if we want them to show up here.  it'd also make the code cleaner.
dnl i'll get to that on another rainy day :) -sf
##ACX_FEATURE([with],[apt-get-command])
dnl ACX_FEATURE([with],[dig-command])
dnl ACX_FEATURE([with],[fping-command])
dnl ACX_FEATURE([with],[mailq-command])
dnl ACX_FEATURE([with],[nslookup-command])
##ACX_FEATURE([with],[ping6-command])
##ACX_FEATURE([with],[ping-command])
dnl ACX_FEATURE([with],[qstat-command])
dnl ACX_FEATURE([with],[rpcinfo-command])
dnl ACX_FEATURE([with],[smbclient-command])
dnl ACX_FEATURE([with],[snmpget-command])
dnl ACX_FEATURE([with],[snmpgetnext-command])
dnl ACX_FEATURE([with],[ssh-command])
dnl ACX_FEATURE([with],[uptime-command])

dnl ACX_FEATURE([with],[proc-meminfo])
dnl ACX_FEATURE([with],[ps-command])
dnl ACX_FEATURE([with],[ps-format])
dnl ACX_FEATURE([with],[ps-cols])
dnl ACX_FEATURE([with],[ps-varlist])

#ACX_FEATURE([with],[ipv6])
#ACX_FEATURE([with],[mysql])
###ACX_FEATURE([with],[openssl])
###ACX_FEATURE([with],[gnutls])
###ACX_FEATURE([enable],[extra-opts])
#ACX_FEATURE([with],[perl])
#ACX_FEATURE([enable],[perl-modules])
#ACX_FEATURE([with],[cgiurl])
#ACX_FEATURE([with],[trusted-path])
#ACX_FEATURE([enable],[libtap])


#ifndef HAVE_INDEX
/* use substitute: https://stackoverflow.com/questions/4091864/c-differences-between-strchr-and-index */
#define index(s,c) strchr(s,c)
#endif

#ifndef HAVE_GETEUID
/* return 0 */
#define geteuid() 0
#endif



AC_OUTPUT()
