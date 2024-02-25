cmake_minimum_required(VERSION 3.16)
project(hwcomposer VERSION 1.0 LANGUAGES C CXX)

set(CMAKE_AUTOMOC ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

find_package(Qt6 REQUIRED COMPONENTS Core Gui WaylandCompositor OpenGL DBus)
find_package(PkgConfig)
pkg_check_modules(EGL REQUIRED egl)
pkg_check_modules(HWC_libhardware REQUIRED libhardware)
pkg_check_modules(HWC_androidheaders REQUIRED android-headers)
pkg_check_modules(HWC_hwcomposerwindow REQUIRED hwcomposer-egl)
pkg_check_modules(HWC_hybriseglplatform REQUIRED hybris-egl-platform)
pkg_check_modules(PKG_sync REQUIRED libsync)

qt_add_library(hwcomposer MODULE
                hwcomposer/main.cpp
                hwcomposer/hwcomposer_context.cpp hwcomposer/hwcomposer_context.h
                hwcomposer/hwcomposer_screeninfo.cpp hwcomposer/hwcomposer_screeninfo.h
                hwcomposer/hwcomposer_backend.cpp hwcomposer/hwcomposer_backend.h
                hwcomposer/hwcomposer_backend_v0.cpp hwcomposer/hwcomposer_backend_v0.h
                hwcomposer/hwcomposer_backend_v10.cpp hwcomposer/hwcomposer_backend_v10.h
                hwcomposer/hwcomposer_backend_v11.cpp hwcomposer/hwcomposer_backend_v11.h
                hwcomposer/qsystrace_selector.h
                hwcomposer/hwcomposer_backend_v20.cpp hwcomposer/hwcomposer_backend_v20.h

                hwcomposer/qeglfsintegration.cpp hwcomposer/qeglfsintegration.h
                hwcomposer/qeglfswindow.cpp hwcomposer/qeglfswindow.h
                hwcomposer/qeglfsbackingstore.cpp hwcomposer/qeglfsbackingstore.h
                hwcomposer/qeglfsscreen.cpp hwcomposer/qeglfsscreen.h
                hwcomposer/qeglfscontext.cpp hwcomposer/qeglfscontext.h
                )

target_compile_definitions(hwcomposer PRIVATE QT_EGL_NO_X11 HWC_PLUGIN_HAVE_HWCOMPOSER1_API=1 HWC_PLUGIN_HAVE_HWCOMPOSER2_API=1)
target_link_libraries(hwcomposer PUBLIC Qt::Core
                                        Qt::CorePrivate
                                        Qt::Gui
                                        Qt::GuiPrivate
                                        Qt::WaylandCompositor
                                        Qt::WaylandCompositorPrivate
                                        Qt::OpenGL 
                                        Qt::DBus
                                        ${EGL_LIBRARIES}
                                        ${PKG_sync_LIBRARIES}
                                        ${HWC_libhardware_LIBRARIES}
                                        ${HWC_androidheaders_LIBRARIES}
                                        ${HWC_hwcomposerwindow_LIBRARIES}
                                        ${HWC_hybriseglplatform_LIBRARIES}
                                        hwc2)

target_include_directories(hwcomposer PUBLIC ${EGL_INCLUDE_DIRS}
                                                ${HWC_libhardware_INCLUDE_DIRS}
                                                ${HWC_androidheaders_INCLUDE_DIRS}
                                                ${HWC_hwcomposerwindow_INCLUDE_DIRS}
                                                ${HWC_hybriseglplatform_INCLUDE_DIRS}
                                                ${PKG_sync_INCLUDE_DIRS})

install(TARGETS hwcomposer
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}/qt6/plugins/platforms/)