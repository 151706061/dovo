cmake_minimum_required(VERSION 2.8)

project(dovo)

set(VERSION "1.0.0")

SET(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -DDEBUG")
SET(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -DDEBUG")

set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MT")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /MTd")

set(Boost_USE_STATIC_LIBS        ON)
set(Boost_USE_MULTITHREADED      ON)
set(Boost_USE_STATIC_RUNTIME     ON)
find_package(Boost REQUIRED thread filesystem)

set(wxWidgets_USE_STATIC   ON)
find_package(wxWidgets COMPONENTS core base adv REQUIRED)
include(${wxWidgets_USE_FILE})

find_package(dcmtk REQUIRED CONFIG HINTS ${DCMTK_DIR})

INCLUDE_DIRECTORIES(${CMAKE_SOURCE_DIR} ${CMAKE_SOURCE_DIR}/sqlite3 ${Boost_INCLUDE_DIRS} ${DCMTK_INCLUDE_DIRS} ${DCMJPEG2000}/include )
LINK_DIRECTORIES(${Boost_LIBRARY_DIRS})

set(dovo_HEADERS	
	src/gui.h
	src/dovo_about.h
	src/dovo_changePatientInfo.h
	src/dovo_destination.h
	src/dovo_mainFrame.h
	src/dovo_searchStatus.h
	src/dovo_sendStatus.h

	src/engine.h
	src/destinationentry.h

	src/dicomscanner.h
)

set(dovo_SRCS
    ${dovo_HEADERS}    
	src/main.cpp
	src/gui.cpp
	src/dovo_about.cpp
	src/dovo_changePatientInfo.cpp
	src/dovo_destination.cpp
	src/dovo_mainFrame.cpp
	src/dovo_searchStatus.cpp
	src/dovo_sendStatus.cpp
	src/engine.cpp
	src/dicomscanner.cpp
	sqlite3/sqlite3.c
	sqlite3/sqlite3_exec_stmt.c
)

#VC++ stuff
add_definitions(-D_UNICODE)
add_definitions(-D_BIND_TO_CURRENT_VCLIBS_VERSION=1)

add_definitions(-DHAVE_SSIZE_T)

ADD_EXECUTABLE(dovo WIN32 ${dovo_SRCS})

IF(WIN32)
  SET(OS_SPECIFIC_LIBS netapi32 wsock32 odbc32 Rpcrt4)
ELSE(WIN32)
  SET(OS_SPECIFIC_LIBS )
ENDIF(WIN32)

TARGET_LINK_LIBRARIES(dovo
	
	${DCMTK_LIBRARIES}
	${wxWidgets_LIBRARIES}
	${Boost_LIBRARIES}
	${OS_SPECIFIC_LIBS}
	)

	