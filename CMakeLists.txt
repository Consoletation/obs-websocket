cmake_minimum_required(VERSION 3.5)
project(obs-websocket VERSION 4.9.0)

set(CMAKE_PREFIX_PATH "${QTDIR}")
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}")
set(CMAKE_INCLUDE_CURRENT_DIR TRUE)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include_directories(SYSTEM "${CMAKE_SOURCE_DIR}/UI/obs-frontend-api")
include_directories(SYSTEM "${CMAKE_SOURCE_DIR}/libobs")
include_directories("${QTDIR}/include")
include_directories("${CMAKE_CURRENT_SOURCE_DIR}/deps/asio/asio/include")
include_directories("${CMAKE_CURRENT_SOURCE_DIR}/deps/websocketpp")

add_definitions(-DASIO_STANDALONE)

if (${CMAKE_SYSTEM_PROCESSOR} MATCHES "arm") 
	set(CMAKE_CXX_FLAGS "-mfpu=neon")
endif()

find_package(Qt5 REQUIRED COMPONENTS Core Widgets)
set(obs-websocket_LIBRARIES
	libobs
	obs-frontend-api
	Qt5::Core
	Qt5::Widgets
	)

# --- Windows-specific build settings and tasks ---
if(WIN32)
	if(MSVC)
		# Enable Multicore Builds and disable FH4 (to not depend on VCRUNTIME140_1.DLL)
		add_definitions(/MP /d2FH4-)
	endif()

	add_definitions(-D_WEBSOCKETPP_CPP11_STL_)
endif()
# --- End of section ---

set(obs-websocket_SOURCES
	src/obs-websocket.cpp
	src/WSServer.cpp
	src/ConnectionProperties.cpp
	src/WSRequestHandler.cpp
	src/WSRequestHandler_General.cpp
	src/WSRequestHandler_Profiles.cpp
	src/WSRequestHandler_Recording.cpp
	src/WSRequestHandler_ReplayBuffer.cpp
	src/WSRequestHandler_SceneCollections.cpp
	src/WSRequestHandler_Scenes.cpp
	src/WSRequestHandler_SceneItems.cpp
	src/WSRequestHandler_Sources.cpp
	src/WSRequestHandler_Streaming.cpp
	src/WSRequestHandler_StudioMode.cpp
	src/WSRequestHandler_Transitions.cpp
	src/WSRequestHandler_Outputs.cpp
	src/WSRequestHandler_MediaControl.cpp
	src/WSEvents.cpp
	src/Config.cpp
	src/Utils.cpp
	src/rpc/RpcRequest.cpp
	src/rpc/RpcResponse.cpp
	src/rpc/RpcEvent.cpp
	src/protocol/OBSRemoteProtocol.cpp
	src/forms/settings-dialog.cpp)

set(obs-websocket_HEADERS
	src/obs-websocket.h
	src/WSServer.h
	src/ConnectionProperties.h
	src/WSRequestHandler.h
	src/WSEvents.h
	src/Config.h
	src/Utils.h
	src/rpc/RpcRequest.h
	src/rpc/RpcResponse.h
	src/rpc/RpcEvent.h
	src/protocol/OBSRemoteProtocol.h
	src/forms/settings-dialog.h)

add_library(obs-websocket MODULE
	${obs-websocket_SOURCES}
	${obs-websocket_HEADERS})

target_link_libraries(obs-websocket
	${obs-websocket_LIBRARIES}
	)

install_obs_plugin_with_data(obs-websocket data)
