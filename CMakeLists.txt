cmake_minimum_required(VERSION 2.8)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
# Set output directories for executables and libraries
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/Builds/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/Builds/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_SOURCE_DIR}/Builds/lib)

SET( PROJ_NAME      "tm-muxer" )
SET( PROJ_PATH      ${CMAKE_SOURCE_DIR} )
SET( PROJ_OUT_PATH  ${CMAKE_BINARY_DIR} )
SET( PROJ_SOURCES   "" )
FILE( GLOB_RECURSE PROJ_SOURCES src/*.cpp )
SET( PROJ_HEADERS   "" )
FILE( GLOB_RECURSE PROJ_HEADERS include/*.h )
SET( PROJ_LIBRARIES "tmm-mpeg2ts")
if(NOT WIN32)
    list(APPEND PROJ_LIBRARIES "rt")
endif()
SET( PROJ_INCLUDES  "include" "tmm-mpeg2ts/include")

PROJECT( ${PROJ_NAME} )

INCLUDE_DIRECTORIES( ${PROJ_INCLUDES} )
ADD_EXECUTABLE (${PROJ_NAME} ${PROJ_SOURCES})

ADD_SUBDIRECTORY(tmm-mpeg2ts)

TARGET_LINK_LIBRARIES( ${PROJ_NAME} ${PROJ_LIBRARIES} tmm-mpeg2ts)

set(CMAKE_CXX_FLAGS "-g -Wall")

#Copy settings and multimedia files
file(GLOB SETTINGS files/project.tmm)
add_custom_command(
        TARGET ${PROJ_NAME} PRE_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_if_different ${SETTINGS} ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${project.tmm}
        COMMENT "Copying settings")
file(GLOB MULTIMEDIAFILES files/*.ts)
foreach(MultimediaFile IN LISTS MULTIMEDIAFILES)
	get_filename_component(FileName "${MultimediaFile}" NAME)
    #cmake_path(GET MultimediaFile FILENAME FileName) #only in Cmake 3.20
    add_custom_command(
            TARGET ${PROJ_NAME} PRE_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy_if_different ${MultimediaFile} ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${FileName}
            COMMENT "Copying shader: ${MultimediaFile}")
endforeach()