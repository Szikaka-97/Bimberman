cmake_minimum_required(VERSION 3.21 FATAL_ERROR)
project(Bimberman VERSION 1.0)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

add_subdirectory(Syzyf-Engine)

file(
	GLOB_RECURSE SOURCE_FILES 
	${PROJECT_SOURCE_DIR}/src/*.c
	${PROJECT_SOURCE_DIR}/src/*.cpp
)

file(
	GLOB_RECURSE HEADER_FILES 
	${PROJECT_SOURCE_DIR}/src/include/*.h
	${PROJECT_SOURCE_DIR}/src/include/*.hpp
)

file(
	GLOB_RECURSE ASSETS_FILES 
	${CMAKE_SOURCE_DIR}/res/*.*
)

set_source_files_properties(${ASSETS_FILES} PROPERTIES HEADER_FILE_ONLY TRUE)

source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${SOURCE_FILES})						   
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${HEADER_FILES})
source_group(TREE ${CMAKE_SOURCE_DIR}         FILES ${ASSETS_FILES})

add_executable(${PROJECT_NAME} ${HEADER_FILES} ${SOURCE_FILES} ${ASSETS_FILES} )

target_link_libraries(${PROJECT_NAME} Syzyf)

target_include_directories(${PROJECT_NAME} PRIVATE src/include)
target_include_directories(${PROJECT_NAME} PRIVATE Syzyf-Engine/src/include)

add_custom_command(
	TARGET ${PROJECT_NAME} PRE_BUILD
	COMMAND ${CMAKE_COMMAND} -E create_symlink
	${CMAKE_SOURCE_DIR}/Syzyf-Engine/res
	${CMAKE_SOURCE_DIR}/res
)

add_custom_command(
	TARGET ${PROJECT_NAME} POST_BUILD
	COMMAND ${CMAKE_COMMAND} -E create_symlink
	${CMAKE_SOURCE_DIR}/res
	${CMAKE_CURRENT_BINARY_DIR}/res
)

if(WIN32)
configure_file(
	${CMAKE_BINARY_DIR}/_deps/joltphysics-build/libJolt.dll
	${CMAKE_CURRENT_BINARY_DIR}/libJolt.dll
	COPYONLY
)
endif()