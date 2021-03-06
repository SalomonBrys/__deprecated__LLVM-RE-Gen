
add_definitions (
	-D__STDC_LIMIT_MACROS
	-D__STDC_CONSTANT_MACROS
	-D_CXX
)

if ( WIN32 )
	add_definitions (
		-D_WINDLL
		-D_WIN
	)
endif ()

if ( NOT MSVC )
	set (
		CMAKE_CXX_FLAGS
		"${CMAKE_CXX_FLAGS} -W -Wall -Wno-unused-parameter"
	)
else ()
	set (
		CMAKE_CXX_FLAGS
		"${CMAKE_CXX_FLAGS} /wd4146 /D_CRT_SECURE_NO_WARNINGS /D_SCL_SECURE_NO_WARNINGS"
	)
endif ()

file (
	GLOB
	source_files
	*
)

llvm_map_components_to_libraries ( REQ_LLVM_LIBRARIES bitwriter core bitwriter interpreter jit native target )

if ( LLVMREGEN_BUILD_SHARED_LIBRARY )
	add_library (
		LLVMREGen
		SHARED
		${source_files}
	)
	
	target_link_libraries ( LLVMREGen ${REQ_LLVM_LIBRARIES} )

	if ( UNIX )
		target_link_libraries ( LLVMREGen dl pthread )
	endif ()
	
	if ( WIN32 )
		install (
			TARGETS LLVMREGen
			DESTINATION bin
			ARCHIVE DESTINATION lib
		)
	else ()
		install (
			TARGETS LLVMREGen
			DESTINATION lib
		)
	endif ()
endif ()

if ( LLVMREGEN_BUILD_STATIC_LIBRARY )
	add_library (
		LLVMREGen-s
		STATIC
		${source_files}
	)

	target_link_libraries ( LLVMREGen-s ${REQ_LLVM_LIBRARIES} )

	if ( UNIX )
		target_link_libraries ( LLVMREGen-s dl pthread )
	endif ()
	
	if ( WIN32 )
		install (
			TARGETS LLVMREGen-s
			DESTINATION bin
			ARCHIVE DESTINATION lib
		)
	else ()
		install (
			TARGETS LLVMREGen-s
			DESTINATION lib
		)
	endif ()
endif ()

if ( LLVMREGEN_BUILD_TESTS )
	add_subdirectory ( ../Test/DevelMain ../Test/DevelMain )
endif ()


install (
	FILES LLVMREGen.h
	DESTINATION include
)
