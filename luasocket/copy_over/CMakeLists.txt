CMAKE_MINIMUM_REQUIRED(VERSION 3.6)

PROJECT("lua-gumbo")

ENABLE_TESTING()
FIND_PACKAGE(PythonInterp 2.7 REQUIRED)

IF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
	SET(PLATFORM_SOCKET_SRC src/wsocket.c)
ELSE((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
	SET(PLATFORM_SOCKET_SRC src/usocket.c)
ENDIF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))

# Build the Lua modules.
ADD_LIBRARY(TARGET_socket_core MODULE
            src/auxiliar.c
            src/buffer.c
            src/compat.c
            src/except.c
            src/inet.c
            src/io.c
            src/luasocket.c
            src/options.c
            src/select.c
            src/tcp.c
            src/timeout.c
            src/udp.c
            ${PLATFORM_SOCKET_SRC})
TARGET_INCLUDE_DIRECTORIES(TARGET_socket_core
                           PRIVATE ${LUA_INCLUDE_DIR})
IF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
	TARGET_LINK_LIBRARIES(TARGET_socket_core ws2_32)
	TARGET_LINK_LIBRARIES(TARGET_socket_core ${LUA_LIBRARIES})
	TARGET_COMPILE_DEFINITIONS(TARGET_socket_core
	                           PRIVATE -DLUASOCKET_INET_PTON -D_WIN32_WINNT=0x501 )
ENDIF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
TARGET_COMPILE_DEFINITIONS(TARGET_socket_core
                           PRIVATE -DLUA_COMPAT_5_2)
# Set the name of the output file to "socket.core".
SET_TARGET_PROPERTIES(TARGET_socket_core PROPERTIES PREFIX "" OUTPUT_NAME "socket.core")
# On mingw link all compiler libraries static.
IF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
	SET_PROPERTY(TARGET TARGET_socket_core PROPERTY LINK_FLAGS "-static -static-libgcc -static-libstdc++")
ENDIF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))


ADD_LIBRARY(TARGET_mime_core MODULE
            src/compat.c
            src/mime.c)
TARGET_INCLUDE_DIRECTORIES(TARGET_mime_core
                           PRIVATE ${LUA_INCLUDE_DIR})
IF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
	TARGET_LINK_LIBRARIES(TARGET_mime_core ${LUA_LIBRARIES})
	TARGET_COMPILE_DEFINITIONS(TARGET_mime_core
	                           PRIVATE -DLUASOCKET_INET_PTON -D_WIN32_WINNT=0x501 )
ENDIF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
TARGET_COMPILE_DEFINITIONS(TARGET_mime_core
                           PRIVATE -DLUA_COMPAT_5_2)
# Set the name of the output file to "mime.core".
SET_TARGET_PROPERTIES(TARGET_mime_core PROPERTIES PREFIX "" OUTPUT_NAME "mime.core")
# On mingw link all compiler libraries static.
IF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
	SET_PROPERTY(TARGET TARGET_mime_core PROPERTY LINK_FLAGS "-static -static-libgcc -static-libstdc++")
ENDIF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))


#ADD_LIBRARY(TARGET_unix_core MODULE
#            src/auxiliar.c
#            src/buffer.c
#            src/compat.c
#            src/io.c
#            src/options.c
#            src/timeout.c
#            src/unix.c
#            src/unixdgram.c
#            src/unixstream.c
#            ${PLATFORM_SOCKET_SRC})
#TARGET_INCLUDE_DIRECTORIES(TARGET_unix_core
#                           PRIVATE ${LUA_INCLUDE_DIR})
#IF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
#	TARGET_LINK_LIBRARIES(TARGET_unix_core ${LUA_LIBRARIES})
#	TARGET_COMPILE_DEFINITIONS(TARGET_unix_core -DLUASOCKET_INET_PTON -D_WIN32_WINNT=0x501 )
#ENDIF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
#TARGET_COMPILE_DEFINITIONS(TARGET_unix_core
#                           PRIVATE -DLUA_COMPAT_5_2)
## Set the name of the output file to "unix.core".
#SET_TARGET_PROPERTIES(TARGET_unix_core PROPERTIES PREFIX "" OUTPUT_NAME "unix.core")
## On mingw link all compiler libraries static.
#IF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
#	SET_PROPERTY(TARGET TARGET_unix_core PROPERTY LINK_FLAGS "-static -static-libgcc -static-libstdc++")
#ENDIF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))


#ADD_LIBRARY(TARGET_serial MODULE
#            src/auxiliar.c
#            src/buffer.c
#            src/compat.c
#            src/io.c
#            src/options.c
#            src/serial.c
#            src/timeout.c
#            ${PLATFORM_SOCKET_SRC})
#TARGET_INCLUDE_DIRECTORIES(TARGET_serial
#                           PRIVATE ${LUA_INCLUDE_DIR})
#IF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
#	TARGET_LINK_LIBRARIES(TARGET_serial ${LUA_LIBRARIES})
#	TARGET_COMPILE_DEFINITIONS(TARGET_serial -DLUASOCKET_INET_PTON -D_WIN32_WINNT=0x501 )
#ENDIF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
#TARGET_COMPILE_DEFINITIONS(TARGET_serial
#                           PRIVATE -DLUA_COMPAT_5_2)
## Set the name of the output file to "serial".
#SET_TARGET_PROPERTIES(TARGET_serial PROPERTIES PREFIX "" OUTPUT_NAME "serial")
## On mingw link all compiler libraries static.
#IF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
#	SET_PROPERTY(TARGET TARGET_serial PROPERTY LINK_FLAGS "-static -static-libgcc -static-libstdc++")
#ENDIF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))


# Install the lua modules.
INSTALL(TARGETS TARGET_socket_core TARGET_mime_core
        EXPORT EXPORT_package
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin
        INCLUDES DESTINATION include)

# Add tests for the modules.
IF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))
	# Here are the MinGW specific tests.
	ADD_TEST(NAME socket_MinGW_DLL_dependencies
	         COMMAND "${PYTHON_EXECUTABLE}" ${TEST_SCRIPT_FOLDER}/mingw_dll_dependencies.py -u lua5.1 -u lua5.2 -u lua5.3 -u lua5.4 $<TARGET_FILE:TARGET_socket_core>)
	ADD_TEST(NAME mime_MinGW_DLL_dependencies
	         COMMAND "${PYTHON_EXECUTABLE}" ${TEST_SCRIPT_FOLDER}/mingw_dll_dependencies.py -u lua5.1 -u lua5.2 -u lua5.3 -u lua5.4 $<TARGET_FILE:TARGET_mime_core>)
#	ADD_TEST(NAME unix_MinGW_DLL_dependencies
#	         COMMAND "${PYTHON_EXECUTABLE}" ${TEST_SCRIPT_FOLDER}/mingw_dll_dependencies.py -u lua5.1 -u lua5.2 -u lua5.3 -u lua5.4 $<TARGET_FILE:TARGET_unix_core>)
#	ADD_TEST(NAME serial_MinGW_DLL_dependencies
#	         COMMAND "${PYTHON_EXECUTABLE}" ${TEST_SCRIPT_FOLDER}/mingw_dll_dependencies.py -u lua5.1 -u lua5.2 -u lua5.3 -u lua5.4 $<TARGET_FILE:TARGET_serial>)
ENDIF((${CMAKE_SYSTEM_NAME} STREQUAL "Windows") AND (${CMAKE_COMPILER_IS_GNUCC}))

# Install all LUA scripts.
INSTALL(FILES src/ltn12.lua src/mime.lua src/socket.lua
        DESTINATION lua)

INSTALL(FILES src/ftp.lua src/http.lua src/smtp.lua src/tp.lua src/url.lua src/headers.lua
        DESTINATION lua/socket)
