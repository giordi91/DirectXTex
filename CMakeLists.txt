cmake_minimum_required(VERSION 3.13.0)

project(DirectXTex)   

    #looking for  files
    file(GLOB_RECURSE SOURCE_FILES "DirectXTex/*.cpp" "DirectXTex/*.h" )
	#list(REMOVE_ITEM SOURCE_FILES "${CMAKE_CURRENT_SOURCE_DIR}/Src/XboxDDSTextureLoader.cpp")
	

    message (STATUS ${CMAKE_CURRENT_SOURCE_DIR})
    include_directories(${CMAKE_CURRENT_SOURCE_DIR}/Inc
						${CMAKE_CURRENT_SOURCE_DIR}/Src
	)

	add_compile_definitions( _UNICODE UNICODE _LIB _WIN32_WINNT=0x0A00 _CRT_STDIO_ARBITRARY_WIDE_SPECIFIERS)

	#making sure to add the common cpp flags, that are defined in the main cpp file
    set(COMMON_CXX_FLAGS "${COMMON_CXX_FLAGS}  /std:c++17 /W4 /arch:AVX2 /fp:fast /MP")
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${COMMON_CXX_FLAGS}")


	MACRO(SOURCE_GROUP_BY_FOLDER target)
	  SET(SOURCE_GROUP_DELIMITER "/")
	  SET(last_dir "")
	  SET(files "")
	  FOREACH(file ${SOURCE_FILES})
		file(RELATIVE_PATH relative_file "${PROJECT_SOURCE_DIR}/{target}" ${file})
		GET_FILENAME_COMPONENT(dir "${relative_file}" PATH)
		IF (NOT "${dir}" STREQUAL "${last_dir}")
		  IF (files)
			SOURCE_GROUP("${last_dir}" FILES ${files})
		  ENDIF (files)
		  SET(files "")
		ENDIF (NOT "${dir}" STREQUAL "${last_dir}")
		SET(files ${files} ${file})
		SET(last_dir "${dir}")
	  ENDFOREACH(file)
	  IF (files)
		SOURCE_GROUP("${last_dir}" FILES ${files})
	  ENDIF (files)
	ENDMACRO(SOURCE_GROUP_BY_FOLDER)

	SOURCE_GROUP_BY_FOLDER(SirEngineThe3rdLib)


    #adding the executable
    add_library(${PROJECT_NAME} STATIC ${SOURCE_FILES} ${INCLUDES_FILES})

	#setting the plug-in folder in the solution
	set_target_properties (${PROJECT_NAME} PROPERTIES
    FOLDER vendors)

	SET(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin)

	#target_link_libraries(${PROJECT_NAME} d3dcompiler DXGI D3D12 imgui)

    file(GLOB_RECURSE TO_COPY  "DirectXTex/*.h" )
    file(GLOB_RECURSE TO_COPY2  "DirectXTex/*.inl" )
	file(COPY ${TO_COPY} DESTINATION "${CMAKE_SOURCE_DIR}/ResourceCompilerPlugins/TextureCompilerPlugin/vendors/includes/DirectXTex" )
	file(COPY ${TO_COPY2} DESTINATION "${CMAKE_SOURCE_DIR}/ResourceCompilerPlugins/TextureCompilerPlugin/vendors/includes/DirectXTex" )
