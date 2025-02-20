cmake_minimum_required(VERSION 3.15)
project(qruler VERSION 0.1.1 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)
#===============================================================================
# Qt
#===============================================================================
# TODO: Qt6
#find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Widgets LinguistTools)
#find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Widgets LinguistTools)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)
set(CMAKE_AUTOUIC ON)
find_package(Qt5 COMPONENTS Widgets LinguistTools REQUIRED)
#===============================================================================
# Project files
#===============================================================================
set(PROJECT_UI_FILES
    src/dialogabout.ui
    src/dialogprefs.ui
)
set(PROJECT_HEADERS
    src/application.hpp
    src/dialogabout.hpp
    src/dialogprefs.hpp
    src/mainwindow.hpp
    src/settings.hpp
)
set(PROJECT_SOURCES
    src/application.cpp
    src/dialogabout.cpp
    src/dialogprefs.cpp
    src/mainwindow.cpp
    src/settings.cpp
)
set(PROJECT_RESOURCES
    resources/resources.qrc
    resources/in/about.info.md.in
)
set(PROJECT_OTHER_FILES
    .github/workflows/build.yml
    README.md
)
file(GLOB PROJECT_TRANSLATION_SOURCES "resources/translations/*")
#===============================================================================
# Configure files
#===============================================================================
include(${PROJECT_NAME}Config.cmake)
include(QtAppResources)

set(PROJECT_ALL_FILES
    ${PROJECT_OTHER_FILES}
    ${PROJECT_RESOURCES}
    ${PROJECT_HEADERS}
    ${PROJECT_SOURCES}
    ${PROJECT_TRANSLATION_SOURCES}
    ${PROJECT_UI_FILES}
    ${PROJECT_QM_FILES}
    ${PROJECT_DESKTOP_FILES}
)
source_group("Other Files" FILES ${PROJECT_OTHER_FILES})
source_group("Resource Files" FILES ${PROJECT_RESOURCES})
source_group("Translation Sources" FILES ${PROJECT_TRANSLATION_SOURCES})
source_group("UI Files" FILES ${PROJECT_UI_FILES})
#===============================================================================
# Application executable
#===============================================================================
# FIXME: QT_VERSION_MAJOR is not defined??
#if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
if(false)
    # Define target properties for Android with Qt 6 as:
    #    set_property(TARGET test APPEND PROPERTY QT_ANDROID_PACKAGE_SOURCE_DIR
    #                 ${CMAKE_CURRENT_SOURCE_DIR}/android)
    # For more information, see https://doc.qt.io/qt-6/qt-add-executable.html#target-creation
    qt_add_executable(${PROJECT_NAME} MANUAL_FINALIZATION ${PROJECT_ALL_FILES})
#   qt_create_translation(PROJECT_QM_FILES ${CMAKE_SOURCE_DIR} ${PROJECT_TS_FILES})
else()
    if(ANDROID)
        # Define properties for Android with Qt 5 after find_package() calls as:
        #    set(ANDROID_PACKAGE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/android")
        add_library(${PROJECT_NAME} SHARED ${PROJECT_ALL_FILES})
    else()
        add_executable(${PROJECT_NAME} ${PROJECT_ALL_FILES})
    endif()
#   qt5_create_translation(PROJECT_QM_FILES ${CMAKE_SOURCE_DIR} ${PROJECT_TS_FILES})
endif()

if(false)
target_link_libraries(${PROJECT_NAME} PRIVATE
    Qt${QT_VERSION_MAJOR}::Widgets
)
endif()
target_link_libraries(${PROJECT_NAME} PRIVATE
    Qt5::Widgets
)
set_target_properties(${PROJECT_NAME} PROPERTIES OUTPUT_NAME "${PROJECT_ID}")
target_compile_definitions(${PROJECT_NAME} PRIVATE
    APPLICATION_NAME="${PROJECT_NAME}"
    ORGANIZATION_NAME="${PROJECT_ORGANIZATION_NAME}"
    ORGANIZATION_DOMAIN="${PROJECT_ORGANIZATION_URL}"
    PROJECT_ID="${PROJECT_ID}"
    PROJECT_DATA_DIR="${CMAKE_INSTALL_FULL_DATADIR}/${PROJECT_ID}"
    PROJECT_ICON_NAME="${PROJECT_ICON_FILE_NAME}"
    PROJECT_ICON_SYSTEM_PATH="${PROJECT_ICON_FILE_PATH}/${PROJECT_ICON_FILE_NAME}"
)
#===============================================================================
# Install application
#===============================================================================
if (UNIX AND NOT APPLE)
    include(GNUInstallDirs)
    install(TARGETS ${PROJECT_NAME} DESTINATION ${CMAKE_INSTALL_BINDIR})
else()
    set_target_properties(test PROPERTIES
        MACOSX_BUNDLE_GUI_IDENTIFIER ${PROJECT_ORGANIZATION_URL}
        MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
        MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
        MACOSX_BUNDLE TRUE
        WIN32_EXECUTABLE TRUE
    )
    install(TARGETS ${PROJECT_NAME}
        BUNDLE DESTINATION .
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    )
endif()
if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(test)
endif()
#===============================================================================
# Project information
#===============================================================================
message(STATUS "
Project name:                     ${PROJECT_NAME}
AppStream ID:                     ${PROJECT_APPSTREAM_ID}
Version:                          ${PROJECT_VERSION}
Qt version:                       ${Qt5Core_VERSION_STRING}
Build type:                       ${CMAKE_BUILD_TYPE}
Install prefix:                   ${CMAKE_INSTALL_PREFIX}
Update translations before build: ${UPDATE_TRANSLATIONS}

CXX Debug flags:                  ${CMAKE_CXX_FLAGS_DEBUG}
CXX Release flags:                ${CMAKE_CXX_FLAGS_RELEASE}
CXX MinSize flags:                ${CMAKE_CXX_FLAGS_MINSIZEREL}
CXX RelWithDebInfo flags:         ${CMAKE_CXX_FLAGS_RELWITHDEBINFO}
")
