project(pam_modules)

function(add_module)
	set(oneValueArgs NAME)
	set(multiValueArgs SOURCES)
	cmake_parse_arguments(add_module "" "${oneValueArgs}" "${multiValueArgs}" ${ARGN})

	set(DYLIB_INSTALL_NAME "/usr/lib/pam/pam_${add_module_NAME}.so.2")
	add_darling_library(pam_${add_module_NAME} SHARED "${add_module_SOURCES}")
	make_fat(pam_${add_module_NAME})
	set_target_properties(pam_${add_module_NAME} PROPERTIES
		SUFFIX ".so.2"
		PREFIX ""
	)
	target_link_libraries(pam_${add_module_NAME} system)
endfunction(add_module)

add_module(NAME akm SOURCES
	modules/pam_aks/pam_aks.c
)
target_link_libraries(pam_akm ${CoreFoundation} pam.2)

install(TARGETS pam_akm
	DESTINATION libexec/darling/usr/lib/pam
	PERMISSIONS OWNER_READ GROUP_READ WORLD_READ
)
