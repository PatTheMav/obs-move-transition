cmake_minimum_required(VERSION 3.16)

project(obs-move-transition VERSION 2.5.2)
add_library(obs-move-transition MODULE)
set(PROJECT_FULL_NAME "Move Transition")
set(MACOS_BUNDLEID "com.exeldro.obs-move-transition")

find_package(libobs REQUIRED)
find_package(obs-frontend-api REQUIRED)
include(external/ObsPluginHelpers.cmake)

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/version.h.in ${CMAKE_CURRENT_SOURCE_DIR}/version.h)

target_sources(obs-move-transition PRIVATE
	audio-move.c
	move-transition.c
	move-transition.h
	move-transition-override-filter.c
	move-source-filter.c
	move-value-filter.c
	easing.c
	easing.h
	version.h)

target_link_libraries(obs-move-transition PRIVATE OBS::libobs OBS::obs-frontend-api)
target_compile_features(obs-move-transition PRIVATE cxx_std_17)

if(OS_WINDOWS)
	configure_file(resource.rc.in move-transition.rc)
	target_sources(obs-move-transition PRIVATE move-transition.rc)

	if(MSVC)
		target_compile_options(obs-move-transition
			PRIVATE
				/MP
				/d2FH4-)
	endif()
# --- End of section ---

# -- macOS specific build settings and tasks --
elseif(OS_MACOS)
	set(MACOSX_PLUGIN_GUI_IDENTIFIER "${MACOS_BUNDLEID}")
	set(MACOSX_PLUGIN_BUNDLE_VERSION "${CMAKE_PROJECT_VERSION}")
	set(MACOSX_PLUGIN_SHORT_VERSION_STRING "1")

	target_compile_options(obs-move-transition
		PRIVATE
			-Wall
			-Wextra
			-Werror-implicit-function-declaration
			-stdlib=libc++
			-fvisibility=default)

	set_target_properties(obs-move-transition PROPERTIES PREFIX "")
# --- End of section ---

# --- Linux-specific build settings and tasks ---
else()
	target_compile_options(obs-move-transition
		PRIVATE
			-Wall
			-Wextra)

	set_target_properties(obs-move-transition PROPERTIES PREFIX "")
endif()
# --- End of section ---

setup_plugin_target(obs-move-transition)
