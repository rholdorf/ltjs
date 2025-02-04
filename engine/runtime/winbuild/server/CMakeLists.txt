cmake_minimum_required (VERSION 3.1.3 FATAL_ERROR)
project (ltjs_server VERSION 0.0.1 LANGUAGES CXX)

option (LTJS_USE_SAFE_SEH "Use Safe Exception Handlers." OFF)

include (ltjs_common)

add_library (${PROJECT_NAME} SHARED)

ltjs_add_defaults (${PROJECT_NAME})

if (LTJS_USE_PCH AND NOT (${CMAKE_VERSION} VERSION_LESS "3.16.0"))
	target_precompile_headers(
		${PROJECT_NAME}
		PRIVATE
			[["bdefs.h"]]
	)
endif ()

target_compile_definitions (
	${PROJECT_NAME}
	PRIVATE
		DE_SERVER_COMPILE
		DIRECTENGINE_COMPILE
		STDLITH_ALLOC_OVERRIDE
		LT15_COMPAT
		NO_PRAGMA_LIBS
		__D3D
		__D3DREND
)

set_target_properties (
	${PROJECT_NAME}
	PROPERTIES
		OUTPUT_NAME ltjs_server
		PREFIX ""
)

set (LTJS_TMP_LINKER_FLAGS "")

if (MSVC)
	set (LTJS_TMP_LINKER_FLAGS "${LTJS_TMP_LINKER_FLAGS} /SUBSYSTEM:WINDOWS")

	if (NOT LTJS_USE_SAFE_SEH)
		set (LTJS_TMP_LINKER_FLAGS "${LTJS_TMP_LINKER_FLAGS} /SAFESEH:NO")
	endif ()

	set (
		path_to_dx_libs
		${CMAKE_SOURCE_DIR}/directx/lib/x86
	)

	set (
		libs
		${path_to_dx_libs}/dxguid.lib
		${path_to_dx_libs}/dplayx.lib
		winmm
	)
endif ()

set_target_properties (
	${PROJECT_NAME}
	PROPERTIES
		LINK_FLAGS "${LTJS_TMP_LINKER_FLAGS}"
)

if (NOT MSVC)
	set (
		libs
		dbghelp
		winmm
		ws2_32
	)
endif ()

target_link_libraries (
	${PROJECT_NAME}
	PRIVATE
		bibendovsky_spul_lib
		ltjs_lib_rez_mgr
		ltjs_lib_lith
		ltjs_lib_std_lith
		ltjs_lib_lt_mem
		ltjs_lib_info
		${libs}
)

target_include_directories (
	${PROJECT_NAME}
	PRIVATE
		.
		../../../../directx/include
		../../../../libs/bibendovsky_spul_lib/include
		../../../../libs/lith
		../../../../libs/stdlith
		../../../libs/rezmgr
		../../../sdk/inc
		../../../sdk/inc/compat
		../../../sdk/inc/physics
		../../../sdk/inc/state_mgr
		../../client/src
		../../kernel/io/src
		../../kernel/mem/src
		../../kernel/net
		../../kernel/net/src
		../../kernel/src
		../../kernel/src/sys/win
		../../lithtemplate
		../../model/src
		../../physics/src
		../../server/src
		../../shared/src
		../../shared/src/sys/win
		../../sound/src
		../../world/src
)

target_sources (
	${PROJECT_NAME}
	PRIVATE
		../../../libs/rezmgr/rezfile.h
		../../../libs/rezmgr/rezhash.h
		../../../libs/rezmgr/rezmgr.h
		../../../libs/rezmgr/reztypes.h
		../../../sdk/inc/iaggregate.h
		../../../sdk/inc/iclientshell.h
		../../../sdk/inc/iltbenchmark.h
		../../../sdk/inc/iltclient.h
		../../../sdk/inc/iltcommon.h
		../../../sdk/inc/iltcsbase.h
		../../../sdk/inc/iltdirectmusic.h
		../../../sdk/inc/iltdrawprim.h
		../../../sdk/inc/iltinfo.h
		../../../sdk/inc/iltmessage.h
		../../../sdk/inc/iltmodel.h
		../../../sdk/inc/iltphysics.h
		../../../sdk/inc/iltrefcount.h
		../../../sdk/inc/iltserver.h
		../../../sdk/inc/iltsoundmgr.h
		../../../sdk/inc/iltspritecontrol.h
		../../../sdk/inc/iltstream.h
		../../../sdk/inc/ilttexinterface.h
		../../../sdk/inc/ilttransform.h
		../../../sdk/inc/iservershell.h
		../../../sdk/inc/lithtech.h
		../../../sdk/inc/ltanimtracker.h
		../../../sdk/inc/ltassert.h
		../../../sdk/inc/ltbasedefs.h
		../../../sdk/inc/ltbasetypes.h
		../../../sdk/inc/ltcodes.h
		../../../sdk/inc/ltengineobjects.h
		../../../sdk/inc/ltinteger.h
		../../../sdk/inc/ltlink.h
		../../../sdk/inc/ltmatrix.h
		../../../sdk/inc/ltmodule.h
		../../../sdk/inc/ltplane.h
		../../../sdk/inc/ltpvalue.h
		../../../sdk/inc/ltquatbase.h
		../../../sdk/inc/ltrect.h
		../../../sdk/inc/ltrenderstyle.h
		../../../sdk/inc/ltserverobj.h
		../../../sdk/inc/ltvector.h
		../../../sdk/inc/physics/aabb.h
		../../../sdk/inc/physics/aabb_tree.h
		../../../sdk/inc/physics/collision_data.h
		../../../sdk/inc/physics/collision_mgr.h
		../../../sdk/inc/physics/collision_object.h
		../../../sdk/inc/physics/coordinate_frame.h
		../../../sdk/inc/physics/math_phys.h
		../../../sdk/inc/physics/matrix.h
		../../../sdk/inc/physics/quaternion.h
		../../../sdk/inc/physics/triangle.h
		../../../sdk/inc/physics/vector.h
		../../../sdk/inc/server_interface.h
		../../client/src/client_filemgr.h
		../../client/src/clientmgr.h
		../../client/src/clientshell.h
		../../client/src/console.h
		../../client/src/predict.h
		../../client/src/setupobject.h
		../../kernel/io/src/sys/win/de_file.h
		../../kernel/io/src/sysfile.h
		../../kernel/mem/src/de_memory.h
		../../kernel/net/src/localdriver.h
		../../kernel/net/src/netmgr.h
		../../kernel/net/src/sys/linux/linux_ltthread.h
		../../kernel/net/src/sys/linux/linux_ltthreadevent.h
		../../kernel/net/src/sys/win/udpdriver.h
		../../kernel/net/src/sys/win/win32_ltthread.h
		../../kernel/net/src/sys/win/win32_ltthreadevent.h
		../../kernel/net/src/syssocket.h
		../../kernel/net/src/sysudpdriver.h
		../../kernel/net/src/sysudpthread.h
		../../kernel/src/dsys.h
		../../kernel/src/ithread.h
		../../kernel/src/sys/win/appresource.h
		../../kernel/src/sys/win/bindmgr.h
		../../kernel/src/sys/win/counter.h
		../../kernel/src/sys/win/dsys_interface.h
		../../kernel/src/sys/win/dutil.h
		../../kernel/src/sys/win/lthread.h
		../../kernel/src/sys/win/ltthread.h
		../../kernel/src/sys/win/render.h
		../../kernel/src/sys/win/streamsim.h
		../../kernel/src/sys/win/stringmgr.h
		../../kernel/src/sys/win/timemgr.h
		../../kernel/src/sys/win/version_resource.h
		../../kernel/src/syscounter.h
		../../kernel/src/syslibraryloader.h
		../../kernel/src/syslthread.h
		../../kernel/src/sysstreamsim.h
		../../kernel/src/syssysteminfo.h
		../../kernel/src/systhread.h
		../../kernel/src/systimer.h
		../../model/src/animtracker.h
		../../model/src/ltb.h
		../../model/src/model.h
		../../model/src/model_ops.h
		../../model/src/modelallocations.h
		../../model/src/transformmaker.h
		../../server/src/classmgr.h
		../../server/src/game_serialize.h
		../../server/src/interlink.h
		../../server/src/ltmessage_server.h
		../../server/src/s_client.h
		../../server/src/s_concommand.h
		../../server/src/s_net.h
		../../server/src/s_object.h
		../../server/src/server_consolestate.h
		../../server/src/server_extradata.h
		../../server/src/server_filemgr.h
		../../server/src/server_loaderthread.h
		../../server/src/serverde_impl.h
		../../server/src/serverevent.h
		../../server/src/serverexception.h
		../../server/src/servermgr.h
		../../server/src/serverobj.h
		../../server/src/sloaderthread.h
		../../server/src/smoveabstract.h
		../../server/src/soundtrack.h
		../../shared/src/bdefs.h
		../../shared/src/build_options.h
		../../shared/src/classbind.h
		../../shared/src/clienthack.h
		../../shared/src/collision.h
		../../shared/src/concommand.h
		../../shared/src/conparse.h
		../../shared/src/debuggeometry.h
		../../shared/src/dhashtable.h
		../../shared/src/dtxmgr.h
		../../shared/src/ftbase.h
		../../shared/src/ftserv.h
		../../shared/src/gamemath.h
		../../shared/src/genericprop_setup.h
		../../shared/src/genltstream.h
		../../shared/src/geometry.h
		../../shared/src/geomroutines.h
		../../shared/src/impl_common.h
		../../shared/src/lightmap_planes.h
		../../shared/src/listqueue.h
		../../shared/src/ltmessage.h
		../../shared/src/lttimer.h
		../../shared/src/motion.h
		../../shared/src/moveobject.h
		../../shared/src/moveplayer.h
		../../shared/src/musicmgr.h
		../../shared/src/nexus.h
		../../shared/src/objectmgr.h
		../../shared/src/packetdefs.h
		../../shared/src/parse_world_info.h
		../../shared/src/pixelformat.h
		../../shared/src/ratetracker.h
		../../shared/src/refobj.h
		../../shared/src/renderinfostruct.h
		../../shared/src/renderobject.h
		../../shared/src/shared_iltcommon.h
		../../shared/src/stacktrace.h
		../../shared/src/staticfifo.h
		../../shared/src/stdlterror.h
		../../shared/src/sys/win/d3dddstructs.h
		../../shared/src/sys/win/renderstruct.h
		../../shared/src/sysddstructs.h
		../../shared/src/version_info.h
		../../sound/src/iltsound.h
		../../sound/src/soundbuffer.h
		../../sound/src/sounddata.h
		../../sound/src/soundinstance.h
		../../sound/src/wave.h
		../../world/src/de_mainworld.h
		../../world/src/de_objects.h
		../../world/src/de_sprite.h
		../../world/src/de_world.h
		../../world/src/fullintersectline.h
		../../world/src/intersect_line.h
		../../world/src/light_table.h
		../../world/src/loadstatus.h
		../../world/src/world_blind_object_data.h
		../../world/src/world_blocker_data.h
		../../world/src/world_client.h
		../../world/src/world_client_bsp.h
		../../world/src/world_interface.h
		../../world/src/world_particle_blocker_data.h
		../../world/src/world_server.h
		../../world/src/world_server_bsp.h
		../../world/src/world_shared_bsp.h
		../../world/src/world_tree.h
		../../world/src/worldtreehelper.h
		resource.h
)

target_sources (
	${PROJECT_NAME}
	PRIVATE
		../../../sdk/inc/ltmodule.cpp
		../../../sdk/inc/ltobjref.cpp
		../../../sdk/inc/ltquatbase.cpp
		../../kernel/io/src/sys/win/de_file.cpp
		../../kernel/mem/src/sys/win/de_memory.cpp
		../../kernel/net/src/localdriver.cpp
		../../kernel/net/src/netmgr.cpp
		../../kernel/net/src/packet.cpp
		../../kernel/net/src/sys/win/udpdriver.cpp
		../../kernel/src/debugging.cpp
		../../kernel/src/server_interface.cpp
		../../kernel/src/sys/win/bindmgr.cpp
		../../kernel/src/sys/win/counter.cpp
		../../kernel/src/sys/win/dsys_interface.cpp
		../../kernel/src/sys/win/dutil.cpp
		../../kernel/src/sys/win/lthread.cpp
		../../kernel/src/sys/win/ltlibraryloader.cpp
		../../kernel/src/sys/win/ltthread.cpp
		../../kernel/src/sys/win/streamsim.cpp
		../../kernel/src/sys/win/stringmgr.cpp
		../../kernel/src/sys/win/systeminfo.cpp
		../../kernel/src/sys/win/timemgr.cpp
		../../model/src/animtracker.cpp
		../../model/src/model.cpp
		../../model/src/model_load.cpp
		../../model/src/modelallocations.cpp
		../../model/src/sys/d3d/d3d_model_load.cpp
		../../model/src/transformmaker.cpp
		../../server/src/classmgr.cpp
		../../server/src/game_serialize.cpp
		../../server/src/interlink.cpp
		../../server/src/ltmessage_server.cpp
		../../server/src/s_client.cpp
		../../server/src/s_concommand.cpp
		../../server/src/s_intersect.cpp
		../../server/src/s_net.cpp
		../../server/src/s_object.cpp
		../../server/src/server_consolestate.cpp
		../../server/src/server_extradata.cpp
		../../server/src/server_filemgr.cpp
		../../server/src/server_iltcommon.cpp
		../../server/src/server_iltmodel.cpp
		../../server/src/server_iltphysics.cpp
		../../server/src/server_iltsoundmgr.cpp
		../../server/src/server_loaderthread.cpp
		../../server/src/serverde_impl.cpp
		../../server/src/serverevent.cpp
		../../server/src/servermgr.cpp
		../../server/src/sloaderthread.cpp
		../../server/src/smoveabstract.cpp
		../../server/src/soundtrack.cpp
		../../server/src/world_server_bsp.cpp
		../../shared/src/bdefs.cpp
		../../shared/src/classbind.cpp
		../../shared/src/collision.cpp
		../../shared/src/compress.cpp
		../../shared/src/concommand.cpp
		../../shared/src/conparse.cpp
		../../shared/src/dhashtable.cpp
		../../shared/src/engine_vars.cpp
		../../shared/src/findobj.cpp
		../../shared/src/ftserv.cpp
		../../shared/src/gamemath.cpp
		../../shared/src/genericprop_setup.cpp
		../../shared/src/genltstream.cpp
		../../shared/src/geometry.cpp
		../../shared/src/geomroutines.cpp
		../../shared/src/impl_common.cpp
		../../shared/src/interface_linkage.cpp
		../../shared/src/leech.cpp
		../../shared/src/lightmap_planes.cpp
		../../shared/src/ltmessage.cpp
		../../shared/src/lttimer.cpp
		../../shared/src/modellt_impl.cpp
		../../shared/src/motion.cpp
		../../shared/src/moveobject.cpp
		../../shared/src/moveplayer.cpp
		../../shared/src/nexus.cpp
		../../shared/src/objectmgr.cpp
		../../shared/src/parse_world_info.cpp
		../../shared/src/ratetracker.cpp
		../../shared/src/shared_iltcommon.cpp
		../../shared/src/shared_iltphysics.cpp
		../../shared/src/spritecontrolimpl.cpp
		../../shared/src/stacktrace.cpp
		../../shared/src/stdlterror.cpp
		../../shared/src/strtools.cpp
		../../shared/src/transformlt_impl.cpp
		../../sound/src/sounddata.cpp
		../../sound/src/wave.cpp
		../../world/src/de_mainworld.cpp
		../../world/src/de_nodes.cpp
		../../world/src/de_objects.cpp
		../../world/src/fullintersectline.cpp
		../../world/src/intersect_line.cpp
		../../world/src/intersectsweptsphere.cpp
		../../world/src/light_table.cpp
		../../world/src/world_blind_object_data.cpp
		../../world/src/world_blocker_data.cpp
		../../world/src/world_blocker_math.cpp
		../../world/src/world_particle_blocker_data.cpp
		../../world/src/world_shared_bsp.cpp
		../../world/src/world_tree.cpp
)

if (MSVC)
	target_sources (
		${PROJECT_NAME}
		PRIVATE
			server.def
			server.rc
	)
endif ()


install (
	TARGETS
		${PROJECT_NAME}
	RUNTIME DESTINATION
		"."
)
