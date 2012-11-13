CMAKE_MINIMUM_REQUIRED(VERSION 2.8)
ENABLE_LANGUAGE(CXX)
SET(CMAKE_VERBOSE_MAKEFILE OFF)

PROJECT(AlienFxLite)

########################################################################
# FIRST PART: GENERATE LED CONTROLLER LIB (C++)
########################################################################

FIND_PACKAGE(PkgConfig REQUIRED)

# Find libusb (1.0 or 0.1)
PKG_CHECK_MODULES(LIBUSB-1.0 libusb-1.0)
IF(LIBUSB-1.0_FOUND)
  SET(LIBUSB_LIB "-lusb-1.0")
ELSE(LIBUSB-1.0_FOUND)
  PKG_CHECK_MODULES(LIBUSB REQUIRED libusb)
  SET(LIBUSB_LIB "-lusb")
ENDIF(LIBUSB-1.0_FOUND)

# Java JNI headers required
FIND_PACKAGE(JNI REQUIRED)

# Useful variable
SET(ALIENLIB_SRC_PATH  "${CMAKE_CURRENT_SOURCE_DIR}/linuxLibSrc")

# Include paths
INCLUDE_DIRECTORIES("${ALIENLIB_SRC_PATH}")
INCLUDE_DIRECTORIES("${JAVA_INCLUDE_PATH};${JAVA_INCLUDE_PATH2}")

# Source and headers of the project
SET(ALIEN_SRC "${ALIENLIB_SRC_PATH}/main.cpp")
SET(ALIEN_HEADERS "${ALIENLIB_SRC_PATH}/uk_co_progger_alienFXLite_led_LEDController.h")

# 32 or 64bits?
if(CMAKE_SIZEOF_VOID_P EQUAL 8)
  SET(ARCH "64")
else(CMAKE_SIZEOF_VOID_P EQUAL 8)
  SET(ARCH "32")
endif(CMAKE_SIZEOF_VOID_P EQUAL 8)

# Alien library
ADD_LIBRARY(Alien${ARCH} SHARED ${ALIEN_SRC} ${ALIEN_HEADERS})
# Link with libusb
TARGET_LINK_LIBRARIES(Alien${ARCH} ${LIBUSB_LIB})

# Install directory
SET(CMAKE_INSTALL_PREFIX ${CMAKE_CURRENT_SOURCE_DIR})
INSTALL(
  TARGETS Alien${ARCH}
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
)

########################################################################
# SECOND PART: GENERATE JAVA GUI
########################################################################

# TO BE DONE