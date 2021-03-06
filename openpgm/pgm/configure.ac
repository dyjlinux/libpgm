# -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

#  Ubuntu 10 : v2.65
#   OSX 10.6 : v2.61
# Solaris 10 : v2.59
AC_PREREQ([2.61])
AC_INIT([OpenPGM], [m4_esyscmd([perl version.pl %major.%minor])], [openpgm-dev@googlegroups.com], [openpgm], [http://code.google.com/p/openpgm/])
AC_CONFIG_SRCDIR([reed_solomon.c])
AC_CONFIG_MACRO_DIR([m4])

#  Ubuntu 10 : v1.11
#   OSX 10.6 : v1.10
# Solaris 10 : v1.8
AM_INIT_AUTOMAKE([1.10 no-define foreign])
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])

AC_SUBST([RELEASE_INFO], [m4_esyscmd([perl version.pl %major.%minor])])
AC_SUBST([VERSION_INFO], [m4_esyscmd([perl version.pl 0:%micro])])

# Checks for programs.
AC_PROG_CC
AC_PROG_CC_C99
AC_PROG_LIBTOOL
AC_PATH_PROG(PERL, perl)
AC_PATH_PROG(PYTHON, python)

# nb: earliest verifiable version is 2.2.
m4_ifdef([LT_PREREQ], [LT_PREREQ([2.2])])
m4_ifdef([LT_INIT], [LT_INIT])
AC_SUBST([LIBTOOL_DEPS])
AC_SUBST(PERL)
AC_SUBST(PYTHON)

# Apply system specific rules.
AC_CANONICAL_HOST
CFLAGS="$CFLAGS -D_REENTRANT"
case "$host_os" in
linux*)
	CFLAGS="$CFLAGS -D_XOPEN_SOURCE=600 -D_BSD_SOURCE"
	;;
solaris*)
	CFLAGS="$CFLAGS -D_XOPEN_SOURCE=600 -D__EXTENSIONS__"
	AC_SEARCH_LIBS([socket], [socket])
	AC_SEARCH_LIBS([gethostname], [nsl])
	AC_SEARCH_LIBS([inet_aton], [resolv])
	AC_SEARCH_LIBS([kstat_open], [kstat])
	;;
*)
	;;
esac

# Checks for libraries.
AC_SEARCH_LIBS([sqrt], [m])
AC_SEARCH_LIBS([pthread_mutex_trylock], [pthread])
AC_SEARCH_LIBS([clock_gettime], [rt])

# Checks for header files.
AC_FUNC_ALLOCA
AC_CHECK_HEADERS([arpa/inet.h fcntl.h float.h inttypes.h libintl.h limits.h locale.h malloc.h memory.h netdb.h netinet/in.h stddef.h stdint.h stdlib.h string.h strings.h sys/ioctl.h sys/param.h sys/socket.h sys/time.h sys/timeb.h syslog.h unistd.h wchar.h])

# Checks for typedefs, structures, and compiler characteristics.
AC_HEADER_STDBOOL
AC_TYPE_UID_T
AC_C_INLINE
AC_TYPE_INT16_T
AC_TYPE_INT32_T
AC_TYPE_INT64_T
AC_TYPE_INT8_T
AC_TYPE_MODE_T
AC_C_RESTRICT
AC_TYPE_SIZE_T
AC_TYPE_SSIZE_T
AC_TYPE_UINT16_T
AC_TYPE_UINT32_T
AC_TYPE_UINT64_T
AC_TYPE_UINT8_T

# Checks for library functions.
# TODO: gettext() fails out-of-the-box from AutoConf.
#AM_GNU_GETTEXT
AC_FUNC_MALLOC
AC_FUNC_MMAP
AC_FUNC_REALLOC
AC_FUNC_STRERROR_R
AC_CHECK_FUNCS([atexit clock_gettime floor ftime gethostbyaddr gethostbyname gethostname gettimeofday inet_ntoa memmove memset regcomp select setenv setlocale socket sqrt stpcpy strcasecmp strchr strdup strerror strncasecmp strpbrk strrchr strstr strtol strtoul strtoull])

# POSIX spinlocks
AC_MSG_CHECKING([for pthread_spinlock])
AC_COMPILE_IFELSE(
	[AC_LANG_PROGRAM([[#include <pthread.h>]],
		[[pthread_spinlock_t spinlock; pthread_spin_lock (&spinlock);]])],
	[AC_MSG_RESULT([yes])
		CFLAGS="$CFLAGS -DCONFIG_HAVE_POSIX_SPINLOCK"],
	[AC_MSG_RESULT([no])])
# NSS protocol lookup
AC_CHECK_FUNC([getprotobyname_r])
if test "x$ac_cv_func_getprotobyname_r" = "xyes"; then
AC_MSG_CHECKING([for 4- or 5-param getprotobyname_r])
AC_COMPILE_IFELSE(
	[AC_LANG_PROGRAM([[#include <netdb.h>]],
		[[getprotobyname_r ((const char*)0, (struct protoent*)0, (char*)0, (size_t)0, (struct protoent**)0);]])],
	[AC_MSG_RESULT([5-param])
		CFLAGS="$CFLAGS -DCONFIG_HAVE_GETPROTOBYNAME_R2"],
	[AC_MSG_RESULT([4-param])
		CFLAGS="$CFLAGS -DCONFIG_HAVE_GETPROTOBYNAME_R"])
fi
# NSS networks lookup, IPv4 only
AC_CHECK_FUNC([getnetent],
		CFLAGS="$CFLAGS -DCONFIG_HAVE_GETNETENT",)
# variadic macros
AC_MSG_CHECKING([for C99 variadic macros])
AC_COMPILE_IFELSE(
	[AC_LANG_PROGRAM([[#include <stdio.h>
#define error(...) fprintf (stderr, __VA_ARGS__)]],
		[[error("moo");]])],
	[AC_MSG_RESULT([yes])
		CFLAGS="$CFLAGS -DCONFIG_HAVE_ISO_VARARGS"],
	[AC_MSG_RESULT([no])])
AC_MSG_CHECKING([for GNU-style variadic macros])
AC_COMPILE_IFELSE(
	[AC_LANG_PROGRAM([[#include <stdio.h>
#define error(x...) fprintf (stderr, x)]],
		[[error("moo");]])],
	[AC_MSG_RESULT([yes])
		CFLAGS="$CFLAGS -DCONFIG_HAVE_GNUC_VARARGS"],
	[AC_MSG_RESULT([no])])
# stack memory api header
AC_MSG_CHECKING([for alloca.h])
AC_COMPILE_IFELSE(
	[AC_LANG_PROGRAM([[#include <alloca.h>]],
		[[void* ptr = alloca (1);]])],
	[AC_MSG_RESULT([yes])
		CFLAGS="$CFLAGS -DCONFIG_HAVE_ALLOCA_H"],
	[AC_MSG_RESULT([no])])
# eventfd API
AC_MSG_CHECKING([for eventfd])
AC_COMPILE_IFELSE(
	[AC_LANG_PROGRAM([[#include <sys/eventfd.h>]],
                [[eventfd (0, 0);]])],
        [AC_MSG_RESULT([yes])
                CFLAGS="$CFLAGS -DCONFIG_HAVE_EVENTFD"],
        [AC_MSG_RESULT([no])])
# useful /proc system
AC_CHECK_FILE([/proc/cpuinfo],
		CFLAGS="$CFLAGS -DCONFIG_HAVE_PROC",)
# example: crash handling
AC_CHECK_FUNC([backtrace],
		CFLAGS="$CFLAGS -DCONFIG_HAVE_BACKTRACE",)
# timing
AC_CHECK_FUNC([pselect],
		CFLAGS="$CFLAGS -DCONFIG_HAVE_PSELECT",)
AC_CHECK_FILE([/dev/rtc],
		CFLAGS="$CFLAGS -DCONFIG_HAVE_RTC",)
AC_MSG_CHECKING([for RDTSC instruction])
case "$host_os" in
darwin*)
	AC_MSG_RESULT([no])
	;;
*)
AC_COMPILE_IFELSE(
	[AC_LANG_PROGRAM(,[[unsigned long lo, hi;
__asm__ __volatile__ ("rdtsc" : "=a" (lo), "=d" (hi));]])],
	[AC_MSG_RESULT([yes])
		CFLAGS="$CFLAGS -DCONFIG_HAVE_TSC"],
	[AC_MSG_RESULT([no])])
	;;
esac
AC_CHECK_FILE([/dev/hpet],
		CFLAGS="$CFLAGS -DCONFIG_HAVE_HPET",)
# event handling
AC_CHECK_FUNC([poll],
		CFLAGS="$CFLAGS -DCONFIG_HAVE_POLL",)
AC_CHECK_FUNC([epoll_ctl],
		CFLAGS="$CFLAGS -DCONFIG_HAVE_EPOLL",)
# interface enumeration
AC_CHECK_FUNC([getifaddrs],
		CFLAGS="$CFLAGS -DCONFIG_HAVE_GETIFADDRS",)
AC_MSG_CHECKING([for struct ifreq.ifr_netmask])
AC_COMPILE_IFELSE(
	[AC_LANG_PROGRAM([[#include <sys/types.h>
#include <ifaddrs.h>]],
		[[struct ifaddrs ifa;
ifa.ifa_netmask = (struct sockaddr*)0;]])],
	[AC_MSG_RESULT([yes])
		CFLAGS="$CFLAGS -DCONFIG_HAVE_IFR_NETMASK"],
	[AC_MSG_RESULT([no])])
# win32 cmsg
AC_CHECK_MEMBER([struct _WSAMSG.name],
		CFLAGS="$CFLAGS -DCONFIG_HAVE_WSACMSGHDR",)
# multicast
AC_MSG_CHECKING([for struct group_req.gr_interface])
AC_COMPILE_IFELSE(
	[AC_LANG_PROGRAM([[#include <netinet/in.h>]],
		[[struct group_req gr;
gr.gr_interface = 0;]])],
	[AC_MSG_RESULT([yes])
		CFLAGS="$CFLAGS -DCONFIG_HAVE_MCAST_JOIN"],
	[AC_MSG_RESULT([no])])
AC_MSG_CHECKING([for struct ip_mreqn.imr_ifindex])
AC_COMPILE_IFELSE(
	[AC_LANG_PROGRAM([[#include <netinet/in.h>]],
		[[struct ip_mreqn mreqn;
mreqn.imr_ifindex = 0;]])],
	[AC_MSG_RESULT([yes])
		CFLAGS="$CFLAGS -DCONFIG_HAVE_IP_MREQN"],
	[AC_MSG_RESULT([no])])
# sprintf, caveat http://savannah.gnu.org/patch/?6848 (ax_c_printf_thsep)
AC_MSG_CHECKING([for printf thousands' grouping])
AC_COMPILE_IFELSE(
	[AC_LANG_PROGRAM(,[[printf ("%'d", 1000000);]])],
	[AC_MSG_RESULT([yes])
		CFLAGS="$CFLAGS -DCONFIG_HAVE_SPRINTF_GROUPING"],
	[AC_MSG_RESULT([no])])
AC_CHECK_FUNC([vasprintf],
		CFLAGS="$CFLAGS -DCONFIG_HAVE_VASPRINTF",)
# symbol linking scope
# nb: sun x86 ld doesn't support DSO visibility but the compiler raises
# warnings and these are easier to detect in autoconf.
save_CFLAGS="$CFLAGS"
CFLAGS="$CFLAGS -Werror"
AC_MSG_CHECKING([for hidden visibility attribute])
AC_COMPILE_IFELSE(
	[AC_LANG_PROGRAM([[#ifdef __SUNPRO_C
__hidden
#else
__attribute__((visibility("hidden")))
#endif
void moo (void) {};]],
		[[moo();]])],
	[AC_MSG_RESULT([yes])
		CFLAGS="$save_CFLAGS -DCONFIG_HAVE_DSO_VISIBILITY"],
	[AC_MSG_RESULT([no])
		CFLAGS="$save_CFLAGS"])

# socket binding
CFLAGS="$CFLAGS -DCONFIG_BIND_INADDR_ANY"
# IP header order as per IP(4) on FreeBSD
AC_MSG_CHECKING([for raw IP sockets ip_{len,off} host byte ordering])
case "$host_os" in
*openbsd*)
	AC_MSG_RESULT([no])
	;;
*bsd*|*darwin*|*osf*|*unixware*)
	AC_MSG_RESULT([yes])
	CFLAGS="$CFLAGS -DCONFIG_HOST_ORDER_IP_LEN -DCONFIG_HOST_ORDER_IP_OFF"
	;;
*)
	AC_MSG_RESULT([no])
	;;
esac
# extended assembler on SPARC
case "$host" in
sparc-sun-solaris*)
	AC_MSG_CHECKING([for SPARC extended assembler])
	AC_COMPILE_IFELSE(
		[AC_LANG_PROGRAM([[#include <stdint.h>
uint32_t add32_with_carry (uint32_t a, uint32_t b) {
        __asm__ ( "addcc %2, %0, %0\n\taddx %0, %%g0, %0" : "=r" (a) : "0" (a), "r" (b) : "cc");
        return a;
}]],
			[[uint32_t c = add32_with_carry (1, 2);]])],
		[AC_MSG_RESULT([yes])],
		[AC_MSG_RESULT([optimization required])
			CFLAGS="$CFLAGS -xO1"])
	;;
*)
	;;
esac
# ticket spinlock friendly: unaligned pointers & atomic ops (excl. Sun Pro)
AC_MSG_CHECKING([for unaligned pointers])
AC_RUN_IFELSE(
	[AC_LANG_PROGRAM([[char* nezumi = "mouse";]],
		[[short x = *(short*)(nezumi + 2)]])],
	[AC_MSG_RESULT([yes])
		pgm_unaligned_pointers=yes],
	[AC_MSG_RESULT([no])
		pgm_unaligned_pointers=no])
AC_MSG_CHECKING([for intrinsic atomic ops])
# AC_PREPROC_IFELSE not always portable
AC_COMPILE_IFELSE(
	[AC_LANG_SOURCE([[#if defined( __GNUC__ ) && ( defined( __i386__ ) || defined( __x86_64__ ) )
/* GCC assembler */
#elif defined( __sun )
/* Solaris intrinsic */
#elif defined( __APPLE__ )
/* Darwin intrinsic */
#elif defined( __GNUC__ ) && ( __GNUC__ * 100 + __GNUC_MINOR__ >= 401 )
/* GCC 4.0.1 intrinsic */
#elif defined( _WIN32 )
/* Windows intrinsic */
#else
#	error "Unsupported atomic ops."
#endif]])],
	[AC_MSG_RESULT([yes])
		if test "$pgm_unaligned_pointers" = yes; then
			CFLAGS="$CFLAGS -DCONFIG_TICKET_SPINLOCK -DCONFIG_DUMB_RWSPINLOCK"
		else
			CFLAGS="$CFLAGS -DCONFIG_TICKET_SPINLOCK"
		fi],
	[AC_MSG_RESULT([no])])

AC_CONFIG_FILES([Makefile "openpgm-${RELEASE_INFO}.pc"])
AC_OUTPUT
