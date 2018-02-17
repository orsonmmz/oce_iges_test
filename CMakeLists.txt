# Developers may wish to set DEBUG_OCE to a value from 1..3 to
# enable informational messages in Debug build. Setting a value
# of 4 or greater will instruct the parser to write a VRML2
# equivalent copy of the input file by invoking the
# SceneGraph->WriteVRML() function and depending on the setup
# the object may attempt to write to a protected directory.
#
# In addition to setting the verbosity via DEBUG_OCE, an
# appropriate WXTRACE value must be set prior to program
# execution to enable the logging:
#
# export WXTRACE="PLUGIN_OCE"
#

cmake_minimum_required(VERSION 3.8)

set( LIBS_OCE TKBinXCAF TKPCAF TKSTEP TKXDESTEP TKIGES TKXDEIGES )
find_package( OCE 0.16 REQUIRED ${LIBS_OCE} )

set( wxWidgets_CONFIG_OPTIONS ${wxWidgets_CONFIG_OPTIONS} --static=no )
find_package( wxWidgets 3.0.0 COMPONENTS core base REQUIRED )
include( ${wxWidgets_USE_FILE} )

include_directories( SYSTEM
    ${OCE_INCLUDE_DIRS}
    ${wxWidgets_INCLUDE_DIRS}
    .
)

message(STATUS "${wxWidgets_INCLUDE_DIRS}")
#add_library( s3d_plugin_oce MODULE
add_executable( s3d_plugin_oce
        oce.cpp
        loadmodel.cpp
        )

#target_link_libraries( s3d_plugin_oce kicad_3dsg ${LIBS_OCE} ${wxWidgets_LIBRARIES} )
target_link_libraries( s3d_plugin_oce ${LIBS_OCE} ${wxWidgets_LIBRARIES} )

if( APPLE )
    # puts library into the main kicad.app bundle in build tree
    set_target_properties( s3d_plugin_oce PROPERTIES
        LIBRARY_OUTPUT_DIRECTORY "${OSX_BUNDLE_BUILD_PLUGIN_DIR}/3d"
    )
endif()

install( TARGETS
    s3d_plugin_oce
    DESTINATION ${KICAD_USER_PLUGIN}/3d
    COMPONENT binary
    )
