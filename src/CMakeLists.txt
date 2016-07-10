#
# leptonica
#

################################################################################

########################################
# SHARED LIBRARY leptonica
########################################

file(GLOB hdr "*.h")
file(GLOB src "*.c")
if (MSVC)
    set_source_files_properties(${src} PROPERTIES LANGUAGE CXX)
endif()

add_library                     (leptonica ${LIBRARY_TYPE} ${src} ${hdr})
set_target_properties           (leptonica PROPERTIES VERSION   ${VERSION_MAJOR}.${VERSION_MINOR})
set_target_properties           (leptonica PROPERTIES SOVERSION ${VERSION_MAJOR}.${VERSION_MINOR})
if (WIN32)
	if (STATIC)
		set_target_properties           (leptonica PROPERTIES OUTPUT_NAME       liblept-static-mtdll-${VERSION_MAJOR}${VERSION_MINOR})
		set_target_properties           (leptonica PROPERTIES DEBUG_OUTPUT_NAME liblept-static-mtdll-${VERSION_MAJOR}${VERSION_MINOR}d)
	endif()
	if (NOT STATIC)
		set_target_properties           (leptonica PROPERTIES OUTPUT_NAME       liblept${VERSION_MAJOR}${VERSION_MINOR})
		set_target_properties           (leptonica PROPERTIES DEBUG_OUTPUT_NAME liblept${VERSION_MAJOR}${VERSION_MINOR}d)
	endif()
endif()

if (NOT STATIC)
    target_compile_definitions  (leptonica PRIVATE -DLIBLEPT_EXPORTS)
endif()
if (GIF_LIBRARY)
    target_link_libraries       (leptonica ${GIF_LIBRARY})
endif()
if (JPEG_LIBRARY)
    target_link_libraries       (leptonica ${JPEG_LIBRARY})
endif()
if (JP2K_FOUND)
    target_link_libraries       (leptonica ${JP2K_LIBRARIES})
endif()
if (PNG_LIBRARY)
    target_link_libraries       (leptonica ${PNG_LIBRARY})
endif()
if (TIFF_LIBRARY)
    target_link_libraries       (leptonica ${TIFF_LIBRARY})
endif()
if (WEBP_FOUND)
    target_link_libraries       (leptonica ${WEBP_LIBRARIES})
endif()
if (ZLIB_LIBRARY)
    target_link_libraries       (leptonica ${ZLIB_LIBRARY})
endif()
if (UNIX)
    target_link_libraries       (leptonica m)
endif()
export(TARGETS leptonica FILE ${CMAKE_BINARY_DIR}/LeptonicaTargets.cmake)

################################################################################
install(
    FILES
        ${hdr} ${CMAKE_BINARY_DIR}/src/endianness.h ${CMAKE_BINARY_DIR}/src/config_auto.h
    DESTINATION
        include/leptonica
)
if (WIN32)
	get_property(debugName TARGET leptonica PROPERTY DEBUG_OUTPUT_NAME)
	get_property(Name TARGET leptonica PROPERTY OUTPUT_NAME)
	install(
		FILES
			${CMAKE_BINARY_DIR}/src/Debug/${debugName}.lib
		DESTINATION
			lib
		CONFIGURATIONS
			Debug
	)
	install(
		FILES
			${CMAKE_BINARY_DIR}/src/Release/${Name}.lib
		DESTINATION
			lib
		CONFIGURATIONS
			Release
	)
	install(
		FILES
			${CMAKE_BINARY_DIR}/LeptonicaConfig.cmake ${CMAKE_BINARY_DIR}/LeptonicaConfig-version.cmake
		DESTINATION
			./
		CONFIGURATIONS
			Release
	)
	if (NOT STATIC)
		install(
			FILES
				${CMAKE_BINARY_DIR}/bin/Debug/${debugName}.dll 
			DESTINATION
				bin
			CONFIGURATIONS
				Debug
		)
		install(
			FILES
				${CMAKE_BINARY_DIR}/bin/Release/${Name}.dll 
			DESTINATION
				bin
			CONFIGURATIONS
				Release
		)
		install(
			FILES
				${CMAKE_BINARY_DIR}/bin/Debug/${debugName}.pdb 
			DESTINATION
				lib
			CONFIGURATIONS
				Debug
		)	
	endif()
endif()
