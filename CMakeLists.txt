project(spice-up)

cmake_minimum_required(VERSION 2.6)

# build UI lib
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)

# disable C compiler warnings
add_definitions(-w)

find_package(PkgConfig)

# either of webkitpackages would do

pkg_check_modules(DEPS REQUIRED
  granite>=0.3
  gthread-2.0
  json-glib-1.0
  gtk+-3.0>=3.9.10
)

set (PKG_DEPS
 granite>=0.3
 gee-0.8
 gtk+-3.0
 json-glib-1.0
)

set (VALA_DEPS
 granite
 gee-0.8
 gtk+-3.0
 json-glib-1.0
)

pkg_check_modules (DEPS REQUIRED ${PKG_DEPS})

add_definitions(${DEPS_CFLAGS})

link_libraries(${DEPS_LIBRARIES})

link_directories(${DEPS_LIBRARY_DIRS})
link_directories(${CMAKE_BINARY_DIR})

include_directories(${CMAKE_BINARY_DIR})
include_directories(${CMAKE_SOURCE_DIR})

set (generated_resources ${generated_resource})

find_package(Vala REQUIRED)
include(ValaVersion)
ensure_vala_version("0.26.0" MINIMUM)

set (DATADIR "${CMAKE_INSTALL_PREFIX}/share")
set (PKGDATADIR "${DATADIR}/spice-up")
set (GETTEXT_PACKAGE "spice-up")
set (RELEASE_NAME "TARS")
set (VERSION "0.7")
set (VERSION_INFO "Release")
set (CMAKE_C_FLAGS "-ggdb")
set (PREFIX ${CMAKE_INSTALL_PREFIX})
set (DOLLAR "$")

configure_file (${CMAKE_SOURCE_DIR}/src/config.vala.cmake ${CMAKE_SOURCE_DIR}/src/config.vala)
add_definitions (-DGETTEXT_PACKAGE=\"${GETTEXT_PACKAGE}\")

add_subdirectory (po)
add_subdirectory (schemas)

include(ValaPrecompile)

vala_precompile(VALA_C
  src/Main.vala
  src/Application.vala
  src/Window.vala
  src/Services/Settings.vala
  src/Services/Slide.vala
  src/Services/SlideManager.vala
  src/Services/FileManager.vala
  src/Services/HistoryManager.vala
  src/Services/Utils.vala
  src/Widgets/Canvas.vala
  src/Widgets/ColorButton.vala
  src/Widgets/EntryCombo.vala
  src/Widgets/Headerbar.vala
  src/Widgets/SlideList.vala
  src/Widgets/DynamicToolbar.vala
  src/Widgets/Welcome.vala

  src/Widgets/CanvasItems/CanvasItem.vala
  src/Widgets/CanvasItems/TextItem.vala
  src/Widgets/CanvasItems/ColorItem.vala
  src/Widgets/CanvasItems/ImageItem.vala
  src/Widgets/CanvasItems/Grabber.vala

  src/Widgets/Toolbars/CanvasBar.vala
  src/Widgets/Toolbars/CommonBar.vala
  src/Widgets/Toolbars/ImageBar.vala
  src/Widgets/Toolbars/ShapeBar.vala
  src/Widgets/Toolbars/TextBar.vala
  src/Widgets/Toolbars/Toolbar.vala
  #src/config.vala
PACKAGES
  ${VALA_DEPS}
OPTIONS
  ${GLOBAL_VALAC_OPTIONS}
 -g
  --vapidir=${CMAKE_SOURCE_DIR}/vapi
  --thread
  --target-glib=2.38 # FIXME check before, and target the same version
)

add_executable(spice-up ${VALA_C} ${generated_resources})

install(TARGETS spice-up RUNTIME
  DESTINATION bin)

install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/com.github.philip-scott.spice-up.desktop
  DESTINATION ${DATADIR}/applications/)

#install assets
file(GLOB assets "${CMAKE_CURRENT_SOURCE_DIR}/data/assets")
install (DIRECTORY ${assets}
  DESTINATION ${DATADIR}/spice-up)

install(FILES "${CMAKE_CURRENT_SOURCE_DIR}/data/symbolic/spiceup-shape-symbolic.svg" DESTINATION ${DATADIR}/icons/hicolor/symbolic/apps/)
install(FILES "${CMAKE_CURRENT_SOURCE_DIR}/data/symbolic/spiceup-shape-symbolic.svg" DESTINATION ${DATADIR}/icons/elementary/actions/symbolic/)
install(FILES "${CMAKE_CURRENT_SOURCE_DIR}/data/symbolic/spiceup-text-symbolic.svg" DESTINATION ${DATADIR}/icons/hicolor/symbolic/apps/)
install(FILES "${CMAKE_CURRENT_SOURCE_DIR}/data/symbolic/spiceup-text-symbolic.svg" DESTINATION ${DATADIR}/icons/elementary/actions/symbolic/)

install(FILES "${CMAKE_CURRENT_SOURCE_DIR}/data/com.github.philip-scott.spice-up.svg"
  DESTINATION ${DATADIR}/pixmaps/)

install (FILES ${CMAKE_CURRENT_SOURCE_DIR}/data/com.github.philip-scott.spice-up.appdata.xml DESTINATION ${DATADIR}/appdata/)

