cmake_minimum_required(VERSION 3.16)

project(wget2)

#OBJECTS = bindtextdom.obj dcgettext.obj dgettext.obj gettext.obj finddomain.obj loadmsgcat.obj localealias.obj textdomain.obj l10nflist.obj explodename.obj dcigettext.obj dcngettext.obj dngettext.obj ngettext.obj plural.obj plural-exp.obj localcharset.obj relocatable.obj langprefs.obj localename.obj log.obj printf.obj osdep.obj intl-compat.obj

add_compile_options("$<$<CXX_COMPILER_ID:MSVC>:/source-charset:utf-8>")

set(CMAKE_C_STANDARD 99) 
set(CMAKE_C_STANDARD_REQUIRED ON) 
set(CMAKE_CXX_STANDARD 17) 
set(CMAKE_CXX_STANDARD_REQUIRED ON) 
set(CMAKE_CXX_EXTENSIONS OFF) 
set(CMAKE_EXPORT_COMPILE_COMMANDS ON) 

set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

set_property(GLOBAL PROPERTY USE_FOLDERS ON) 

#include(cmake/dep_dotnet.cmake)
#include(${WGET2_TOP_DIR}/cmake/dep_check_libs.cmake)
#include(${WGET2_TOP_DIR}/cmake/dep_3rdlib_files.cmake)

#gen_dep_lib_dir(all_dep_lib_dir )
#set(dep_libs ${DepLibs})

#include(${WGET2_TOP_DIR}/cmake/dep_libpcre.cmake)

#include(${WGET2_TOP_DIR}/cmake/find_libpcre.cmake)
#include(${WGET2_TOP_DIR}/cmake/find_sdl_release.cmake)
#include(${WGET2_TOP_DIR}/cmake/find_nettle.cmake)

#set                (AllDepDotNetLibs   System    System.Core    System.Data   System.Drawing    System.Windows.Forms ) #   System.ComponentModel
#gen_dotnet_lib_opts(GenDepOpts         ${AllDepDotNetLibs}  )
#set                (ProjDepOpts        ${AllDepOpts})

set(ENV{HomeDir}  ${CMAKE_CURRENT_LIST_DIR}/out/${CMAKE_SYSTEM_NAME})


set (TOP_SRC_DIR ${CMAKE_CURRENT_SOURCE_DIR}) #CMAKE_SOURCE_DIR
set (ALL_SRC_DIR ${TOP_SRC_DIR}/src/)

set (WGET2_BIN_DIR  ${CMAKE_CURRENT_BINARY_DIR})
set (WGET2_TOP_DIR  ${CMAKE_CURRENT_SOURCE_DIR})
set (WGET2_SRC_DIR  ${WGET2_TOP_DIR}/src)
set (WGET2_INC_DIRS ${WGET2_BIN_DIR}     ${WGET2_TOP_DIR}/     ${WGET2_TOP_DIR}/lib    ${WGET2_TOP_DIR}/src     ${WGET2_TOP_DIR}/libwget  )




if(CMAKE_SIZEOF_VOID_P EQUAL 8)
    message(STATUS "64-bit _O_SRCS: ${_O_SRCS}")
elseif(CMAKE_SIZEOF_VOID_P EQUAL 4)
    message(STATUS "32-bit _O_SRCS: ${_O_SRCS}")
else()
    message(STATUS "Unknown architecture")
endif()

file(GLOB_RECURSE LIB_SRCS_LIST     RELATIVE ${WGET2_TOP_DIR} "${WGET2_TOP_DIR}/lib/*.c"         "${WGET2_TOP_DIR}/lib/*.h")

file(GLOB_RECURSE LIBWGET_SRCS_LIST RELATIVE ${WGET2_TOP_DIR} "${WGET2_TOP_DIR}/libwget/*.c"     "${WGET2_TOP_DIR}/libwget/*.h")

file(GLOB_RECURSE WGET2_SRCS_LIST   RELATIVE ${WGET2_TOP_DIR} "${WGET2_SRC_DIR}/*.c"             "${WGET2_SRC_DIR}/*.h")

file(GLOB         WGET2_APP_LIST    RELATIVE ${WGET2_TOP_DIR} "${WGET2_SRC_DIR}/examples/http_get.c"  )

#list(REMOVE_ITEM  WGET2_SRCS_LIST ${WGET2_SRC_DIR}/os2compat.c)


source_group(TREE ${WGET2_TOP_DIR}    FILES ${WGET2_SRCS_LIST}   ${LIBWGET_SRCS_LIST})

list(REMOVE_ITEM  WGET2_SRCS_LIST ${WGET2_APP_LIST})

file(GLOB_RECURSE WGET2_HDRS_LIST RELATIVE ${WGET2_TOP_DIR} ${WGET2_SRC_DIR}/*.h)

#RESOURCES = intl.res

message("[INFO] WGET2_SRCS_LIST:${WGET2_SRCS_LIST}")



set(LIBPCRE_DEFS      PCRE_STATIC)

include_directories(${WGET2_SRC_DIR})

configure_file(${WGET2_TOP_DIR}/config.h.in             ${WGET2_BIN_DIR}/config.h)

#CFLAGS = /arch:IA32 $(MFLAGS) $(WARN_CFLAGS) $(OPTIMFLAGS)  -D LOCALEDIR=\"$(IIlocaledir)\" -DLOCALE_ALIAS_PATH=\"$(IIaliaspath)\" -DIN_wget2 -DENABLE_RELOCATABLE=1 -D IN_LIBRARY -D INSTALLDIR=\"$(IIbindir)\" -DNO_XMALLOC -Dset_relocation_prefix=wget2_set_relocation_prefix -Drelocate=wget2_relocate -DDEPENDS_ON_LIBICONV=1

set (sys_dep_libs           setupapi winmm imm32 version                                )
set (WGET2_DEP_LIBS   ${LIBPCRE_LIBRARY}    ${NETTLE_LIBRARIES}   ${SDL_LIBRARIES}   )

#set (WGET2_DEP_DEFS  LOCALEDIR="" LOCALE_ALIAS_PATH="" HAVE_CONFIG_H IN_WGET2 ENABLE_RELOCATABLE=0 IN_LIBRARY INSTALLDIR="" NO_XMALLOC  DEPENDS_ON_LIBICONV=0  )

set                       (  WGET2_STATIC              wget2_static)
add_library               (${WGET2_STATIC}   STATIC    ${WGET2_SRCS_LIST}   ${LIBWGET_SRCS_LIST})
target_compile_definitions(${WGET2_STATIC}   PRIVATE   ${WGET2_DEP_DEFS}    ${LIBPCRE_DEFS}  WGET2_LIB_STATIC)
target_include_directories(${WGET2_STATIC}   PUBLIC    ${WGET2_INC_DIRS} )
target_link_directories   (${WGET2_STATIC}   PUBLIC    ${WGET2_LIB_DIRS} )
target_link_libraries     (${WGET2_STATIC}             ${WGET2_DEP_LIBS}    ${sys_dep_libs}             )
target_compile_options    (${WGET2_STATIC}   PRIVATE     /Z7     /W4          )
set_target_properties     (${WGET2_STATIC}   PROPERTIES CLEAN_DIRECT_OUTPUT 1     OUTPUT_NAME "wget2"   )

set                       (  WGET2_SHARED             wget2_shared)
add_library               (${WGET2_SHARED}   SHARED    ${WGET2_SRCS_LIST}   ${LIBWGET_SRCS_LIST})
target_compile_definitions(${WGET2_SHARED}   PRIVATE   ${WGET2_DEP_DEFS}    ${LIBPCRE_DEFS}  WGET2_LIB_EXPORTS)
target_include_directories(${WGET2_SHARED}   PUBLIC    ${WGET2_INC_DIRS} )
target_link_directories   (${WGET2_SHARED}   PUBLIC    ${WGET2_LIB_DIRS} )
target_link_libraries     (${WGET2_SHARED}             ${WGET2_DEP_LIBS}    ${sys_dep_libs}             ) #  ${sys_dep_libs}
target_compile_options    (${WGET2_SHARED}   PRIVATE     /Z7     /W4          )
set_target_properties     (${WGET2_SHARED}   PROPERTIES CLEAN_DIRECT_OUTPUT 1     OUTPUT_NAME "wget2"   )
set_target_properties     (${WGET2_SHARED}   PROPERTIES PUBLIC_HEADER             "${WGET2_HDRS_LIST}")

set                       (  WGET2_APP                wget2_app)
add_executable            (${WGET2_APP}               ${WGET2_APP_LIST}     ${LIBWGET_SRCS_LIST}  )
target_compile_definitions(${WGET2_APP}   PRIVATE     ${WGET2_DEP_DEFS}     ${LIBPCRE_DEFS}       )
target_include_directories(${WGET2_APP}   PUBLIC      ${WGET2_INC_DIRS} )
target_link_directories   (${WGET2_APP}   PUBLIC      ${WGET2_LIB_DIRS} )
#target_link_libraries     (${WGET2_APP}               ${WGET2_STATIC}   )  # WGET2_LIB_STATIC
target_link_libraries     (${WGET2_APP}               ${WGET2_SHARED}   )  # must be REALSE

SET(install_targets ${WGET2_STATIC}    ${WGET2_SHARED}    ${WGET2_APP})
SET(install_hdr_dir include)

INSTALL(TARGETS ${install_targets}
        RUNTIME DESTINATION bin
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        PUBLIC_HEADER DESTINATION ${install_hdr_dir}
       )

install(FILES      ${WGET2_HDRS_LIST} DESTINATION ${install_hdr_dir})
#install(DIRECTORY  "inc_sub_dir"            DESTINATION ${install_hdr_dir} FILES_MATCHING PATTERN "*.h")