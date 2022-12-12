project(pam_modules)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/common)

function(add_module)
	set(oneValueArgs NAME)
	set(multiValueArgs SOURCES DEPENDS)
	cmake_parse_arguments(add_module "" "${oneValueArgs}" "${multiValueArgs}" ${ARGN})

	set(DYLIB_INSTALL_NAME "/usr/lib/pam/pam_${add_module_NAME}.so.2")
	add_darling_library(pam_${add_module_NAME} SHARED "${add_module_SOURCES}")
	make_fat(pam_${add_module_NAME})
	set_target_properties(pam_${add_module_NAME} PROPERTIES
		SUFFIX ".so.2"
		PREFIX ""
	)
	target_link_libraries(pam_${add_module_NAME} system pam.2 ${add_module_DEPENDS})
endfunction(add_module)

add_module(
	NAME akm
	SOURCES
		modules/pam_aks/pam_aks.c
	DEPENDS
		CoreFoundation
)

add_module(
	NAME env
	SOURCES
		modules/pam_env/pam_env.c
)

add_module(
	NAME group
	SOURCES
		modules/pam_group/pam_group.c
)

add_module(
	NAME krb5
	SOURCES
		modules/pam_krb5/pam_krb5.c
		common/Common.c
	DEPENDS
		OpenDirectory
		CoreFoundation
		Heimdal
)

add_module(
	NAME ntlm
	SOURCES
		modules/pam_ntlm/pam_ntlm.c
		common/Common.c
	DEPENDS
		OpenDirectory
		CoreFoundation
		GSS
)

add_module(
	NAME launchd
	SOURCES
		modules/pam_launchd/pam_launchd.c
	DEPENDS
		Security
)

add_module(
	NAME localauthentication
	SOURCES
		modules/pam_localauthentication/pam_localauthentication.c
	DEPENDS
		Foundation
		CoreFoundation
		Security
		LocalAuthentication # instead of coreauthd_client
)

add_module(
	NAME mount
	SOURCES
		modules/pam_mount/pam_mount.c
		common/Common.c
	DEPENDS
		sandbox_not_system
		OpenDirectory
		NetFS
		CoreFoundation
)

add_module(
	NAME nologin
	SOURCES
		modules/pam_nologin/pam_nologin.c
)

add_module(
	NAME opendirectory
	SOURCES
		modules/pam_opendirectory/pam_opendirectory.m
		common/Common.c
	DEPENDS
		Foundation
		OpenDirectory
		ConfigurationProfiles
		objc
)

add_module(
	NAME rootok
	SOURCES
		modules/pam_rootok/pam_rootok.c
)

add_module(
	NAME sacl
	SOURCES
		modules/pam_sacl/pam_sacl.c
)

add_module(
	NAME self
	SOURCES
		modules/pam_self/pam_self.c
)

add_module(
	NAME smartcard
	SOURCES
		modules/pam_smartcard/pam_smartcard.c
		modules/pam_smartcard/attribute_matching.c
		modules/pam_smartcard/hash_matching.c
		modules/pam_smartcard/scmatch_evaluation.c
		common/Common.c
	DEPENDS
		DirectoryService
		Security
		CoreFoundation
		OpenDirectory
		CryptoTokenKit # instead of ctkclient and ctkloginhelper
)

add_module(
	NAME tid
	SOURCES
		modules/pam_tid/pam_tid.c
	DEPENDS
		Foundation
		LocalAuthentication
		DirectoryService
		Security
		CoreFoundation
		OpenDirectory
		#LocalAuthentication # instead of coreauthd_client
)

add_module(
	NAME uwtmp
	SOURCES
		modules/pam_uwtmp/pam_uwtmp.c
)

install(
	TARGETS
		pam_akm
		pam_env
		pam_group
		pam_krb5
		pam_launchd
		pam_localauthentication
		pam_mount
		pam_nologin
		pam_ntlm
		pam_opendirectory
		pam_rootok
		pam_sacl
		pam_self
		pam_smartcard
		pam_tid
		pam_uwtmp
	DESTINATION libexec/darling/usr/lib/pam
	PERMISSIONS OWNER_READ GROUP_READ WORLD_READ
)

install(
	FILES
		modules/pam_aks/authorization_aks
		modules/pam_aks/screensaver_aks
		modules/pam_localauthentication/authorization_la
		modules/pam_localauthentication/authorization_lacont
		modules/pam_localauthentication/screensaver_la
		modules/pam_smartcard/authorization_ctk
		modules/pam_smartcard/screensaver_ctk
	DESTINATION libexec/darling/private/etc/pam.d
)
