add_definitions(-DQT_PLUGIN)
add_definitions(-DQT_NO_DEBUG)
add_definitions(-DQT_SHARED)

find_package(Qt5UiPlugin REQUIRED)
find_package(Qt5Designer REQUIRED)

set(QT_USE_QTDESIGNER ON)

########################################################
# Files

set (QGIS_CUSTOMWIDGETS_SRCS
  qgiscustomwidgets.cpp
  qgsauthconfigselectplugin.cpp
  qgscollapsiblegroupboxplugin.cpp
  qgscolorbuttonplugin.cpp
  qgscheckablecomboboxplugin.cpp
  qgsdatetimeeditplugin.cpp
  qgsdockwidgetplugin.cpp
  qgsdoublespinboxplugin.cpp
  qgsexpressionbuilderwidgetplugin.cpp
  qgsextentgroupboxplugin.cpp
  qgsexternalresourcewidgetplugin.cpp
  qgsfeaturelistcomboboxplugin.cpp
  qgsfeaturepickerwidgetplugin.cpp
  qgsfieldcomboboxplugin.cpp
  qgsfieldexpressionwidgetplugin.cpp
  qgsfilewidgetplugin.cpp
  qgsfilterlineeditplugin.cpp
  qgsfontbuttonplugin.cpp
  qgsmaplayercomboboxplugin.cpp
  qgsopacitywidgetplugin.cpp
  qgspasswordlineeditplugin.cpp
  qgsprojectionselectionwidgetplugin.cpp
  qgspropertyoverridebuttonplugin.cpp
  qgsrasterbandcomboboxplugin.cpp
  qgsrelationeditorwidgetplugin.cpp
  qgsrelationreferencewidgetplugin.cpp
  qgsscalerangewidgetplugin.cpp
  qgsscalewidgetplugin.cpp
#  qgsscrollareawidgetplugin.cpp
  qgsspinboxplugin.cpp
  qgssymbolbuttonplugin.cpp
)

set (QGIS_CUSTOMWIDGETS_MOC_HDRS
  qgiscustomwidgets.h
  qgsauthconfigselectplugin.h
  qgscollapsiblegroupboxplugin.h
  qgscolorbuttonplugin.h
  qgscheckablecomboboxplugin.h
  qgsdatetimeeditplugin.h
  qgsdockwidgetplugin.h
  qgsdoublespinboxplugin.h
  qgsexpressionbuilderwidgetplugin.h
  qgsextentgroupboxplugin.h
  qgsexternalresourcewidgetplugin.h
  qgsfeaturelistcomboboxplugin.h
  qgsfeaturepickerwidgetplugin.h
  qgsfieldcomboboxplugin.h
  qgsfieldexpressionwidgetplugin.h
  qgsfilewidgetplugin.h
  qgsfilterlineeditplugin.h
  qgsfontbuttonplugin.h
  qgsmaplayercomboboxplugin.h
  qgsopacitywidgetplugin.h
  qgspasswordlineeditplugin.h
  qgsprojectionselectionwidgetplugin.h
  qgspropertyoverridebuttonplugin.h
  qgsrasterbandcomboboxplugin.h
  qgsrelationeditorwidgetplugin.h
  qgsrelationreferencewidgetplugin.h
  qgsscalerangewidgetplugin.h
  qgsscalewidgetplugin.h
#  qgsscrollareawidgetplugin.h
  qgsspinboxplugin.h
  qgssymbolbuttonplugin.h
)

if(MSVC)
  set_source_files_properties(${QGIS_CUSTOMWIDGETS_MOC_SRCS} PROPERTIES COMPILE_FLAGS "/wd4512 /wd4996" )
else()
  set_source_files_properties(${QGIS_CUSTOMWIDGETS_MOC_SRCS} PROPERTIES COMPILE_FLAGS "-Wno-deprecated-declarations" )
endif()


# left commented as there is no UI file yet
# set(QGIS_CUSTOMWIDGETS_UI_HDRS
#   ${CMAKE_CURRENT_BINARY_DIR}/../ui/ui_qgsXXXwidget.h
# )

include_directories(
  ${CMAKE_SOURCE_DIR}/src/core
  ${CMAKE_SOURCE_DIR}/src/core/raster
  ${CMAKE_SOURCE_DIR}/src/core/auth
  ${CMAKE_SOURCE_DIR}/src/core/expression
  ${CMAKE_SOURCE_DIR}/src/core/geometry
  ${CMAKE_SOURCE_DIR}/src/core/labeling
  ${CMAKE_SOURCE_DIR}/src/core/metadata
  ${CMAKE_SOURCE_DIR}/src/core/symbology
  ${CMAKE_SOURCE_DIR}/src/core/effects
  ${CMAKE_SOURCE_DIR}/src/core/layertree
  ${CMAKE_SOURCE_DIR}/src/core/textrenderer
  ${CMAKE_SOURCE_DIR}/src/gui
  ${CMAKE_SOURCE_DIR}/src/gui/attributetable
  ${CMAKE_SOURCE_DIR}/src/gui/auth
  ${CMAKE_SOURCE_DIR}/src/gui/codeeditors
  ${CMAKE_SOURCE_DIR}/src/gui/editorwidgets
  ${CMAKE_SOURCE_DIR}/src/gui/editorwidgets/core
  ${CMAKE_SOURCE_DIR}/src/gui/raster

  ${CMAKE_BINARY_DIR}/src/core
  ${CMAKE_BINARY_DIR}/src/core/raster
  ${CMAKE_BINARY_DIR}/src/gui
  ${CMAKE_BINARY_DIR}/src/customwidgets
  ${CMAKE_SOURCE_DIR}/external
  ${CMAKE_SOURCE_DIR}/external/nlohmann
  ${CMAKE_CURRENT_BINARY_DIR}/../ui
)
include_directories(SYSTEM
  ${GEOS_INCLUDE_DIR}
  ${QSCINTILLA_INCLUDE_DIR}
  ${Qt5UiPlugin_INCLUDE_DIRS}
  ${Qt5Designer_INCLUDE_DIRS}
)

QT5_WRAP_CPP(QGIS_CUSTOMWIDGETS_MOC_SRCS ${QGIS_CUSTOMWIDGETS_MOC_HDRS})

#############################################################
# qgis_customwidgets library

add_library(qgis_customwidgets SHARED ${QGIS_CUSTOMWIDGETS_SRCS} ${QGIS_CUSTOMWIDGETS_MOC_SRCS} ${QGIS_CUSTOMWIDGETS_MOC_HDRS})

set_target_properties(qgis_customwidgets PROPERTIES
  VERSION ${COMPLETE_VERSION}
  SOVERSION ${COMPLETE_VERSION}
  )

# make sure that UI files will be processed first
add_dependencies(qgis_customwidgets ui)

target_link_libraries(qgis_customwidgets qgis_gui)

GENERATE_EXPORT_HEADER(
   qgis_customwidgets
   BASE_NAME CUSTOMWIDGETS
   EXPORT_FILE_NAME qgis_customwidgets.h
)

set(QGIS_CUSTOMWIDGETS_INSTALL_HDRS ${QGIS_CUSTOMWIDGETS_MOC_HDRS} ${CMAKE_CURRENT_BINARY_DIR}/qgis_customwidgets.h)

# install
install(TARGETS qgis_customwidgets
  RUNTIME DESTINATION ${QT_PLUGINS_DIR}/designer
  LIBRARY DESTINATION ${QT_PLUGINS_DIR}/designer
)

# TODO: apple
install(FILES ${QGIS_CUSTOMWIDGETS_INSTALL_HDRS} DESTINATION ${QGIS_INCLUDE_DIR})
