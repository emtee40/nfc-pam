AC_INIT(pam_nfc, 0.1.4)

AC_CONFIG_MACRO_DIR([m4])

AM_INIT_AUTOMAKE
m4_ifdef([AM_SILENT_RULES],[AM_SILENT_RULES([yes])])

m4_ifndef([PKG_PROG_PKG_CONFIG],
	  [m4_fatal([Could not locate the pkg-config autoconf macros])]
)

AC_PROG_CC
AC_PROG_CPP
AC_PROG_INSTALL
AM_PROG_LIBTOOL

AC_CHECK_LIB(dl, main)

#----------------------------------------------------------------------
# libnfc Checks
LIBNFC_REQUIRED_VERSION=1.7.0
PKG_CHECK_MODULES([libnfc], [libnfc >= $LIBNFC_REQUIRED_VERSION], [], [AC_MSG_ERROR([libnfc >= $LIBNFC_REQUIRED_VERSION is mandatory.])])
#----------------------------------------------------------------------

# PAM Checks
AC_CHECK_HEADER(security/pam_modules.h,
		AC_DEFINE(HAVE_SECURITY_PAM_MODULES_H),
		have_security_pam_modules=no)

AC_CHECK_HEADER(security/_pam_macros.h,
		AC_DEFINE(HAVE_SECURITY__PAM_MACROS_H),
		have_security__pam_macros=no)

AC_CHECK_HEADER(security/pam_appl.h,
		AC_DEFINE(HAVE_SECURITY_PAM_APPL_H),
		have_security_pam_appl=no)

AC_CHECK_HEADER(security/openpam.h,
		AC_DEFINE(HAVE_SECURITY_OPENPAM_H),
		have_security_openpam=no,
		[pam_types.h])

AS_IF([test "$have_security_pam_modules" = "no" -a \
	"$have_security__pam_macros" = "no" -a \
	"$have_security_pam_appl" = "no" -a \
	"$have_security_openpam" = "no"],
	[AC_MSG_ERROR("The PAM headers are missing")]
)
old_LIBS=$LIBS
AC_CHECK_LIB(pam, pam_start, have_pam=yes, have_pam=no)
AS_IF([test "$have_pam" = "no"],
	[AC_MSG_ERROR("The PAM library is missing")]
)
LIBS=$old_LIBS

#----------------------------------------------------------------------
# PAM Module Directory
AC_ARG_WITH([pam-dir], [AS_HELP_STRING([--with-pam-dir=DIR],
	    [directory to install pam module in])], [],
	    [with_pam_dir='${prefix}/lib/security'])
PAM_DEST_DIR="$with_pam_dir"
AC_SUBST(PAM_DEST_DIR)

#----------------------------------------------------------------------
# libcrypt Checks
AC_CHECK_HEADERS(crypt.h, AC_DEFINE(HAVE_CRYPT_H))
AC_CHECK_LIB(crypt, crypt)

AC_CHECK_HEADERS(sys/perm.h, AC_DEFINE(HAVE_SYS_PERM_H))

#----------------------------------------------------------------------
# Backend
AC_ARG_WITH([backend], [AS_HELP_STRING([--with-backend=BACKEND],
	    [Backend to use for authorization storage])], [],
	    [with_backend='config'])
AS_CASE(["$with_backend"],
	[config], [AC_DEFINE(PAM_NFC_BACKEND_CONFIG)],
	[AC_ERROR(Unknown backend '$with_backend'.)]
)

# --enable-debug support (default:no)
AC_ARG_ENABLE([debug],AS_HELP_STRING([--enable-debug],[Debug flags]),[enable_debug=$enableval],[enable_debug="no"])
 
AC_MSG_CHECKING(for debug flag)
AC_MSG_RESULT($enable_debug)
 
AS_IF([test x"$enable_debug" = "xyes"],
  [CFLAGS="$CFLAGS -g3 -Wall -DDEBUG -pedantic"]
)
AC_SUBST([DEBUG_CFLAGS])

AC_OUTPUT(Makefile)
