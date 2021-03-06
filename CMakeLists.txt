CMAKE_MINIMUM_REQUIRED(VERSION 2.8)

SET(CMAKE_CXX_FLAGS "-fno-stack-protector -Wall -Wextra -Wno-unused-parameter -g -O3 -std=gnu++11 -march=native -msse4.2 -I${CMAKE_CURRENT_SOURCE_DIR}/src")
SET(CMAKE_C_FLAGS "-fno-stack-protector -Wall -Wextra -Wno-unused-parameter -g -O3 -std=gnu11 -march=native -msse4.2")
SET(CMAKE_EXE_LINKER_FLAGS "-rdynamic") # to access functions from luajit

SET(PRJ MoonGen)

SET(FILES
	src/main.c
	src/memory.c
	src/task.c
	src/device.c
	src/util.c
	src/lifecycle.c
	src/timestamping.c
	src/debug.c
	src/bitmask.c
	src/lpm_l.c
	src/distribute.c
	src/barrier.cpp
	src/task-results.cpp
	src/pipe.cpp
	src/lock.cpp
	src/namespaces.cpp
	src/ring.c
	src/rate_limiter.cpp
)

SET(DPDK_LIBS
	ethdev       rte_distributor  rte_kni       rte_mbuf      rte_pmd_bond   rte_pmd_ring         rte_power  rte_timer
	rte_acl      rte_eal          rte_kvargs    rte_mempool   rte_pmd_e1000  rte_pmd_virtio       rte_ring
	rte_cfgfile  rte_hash         rte_lpm       rte_meter     rte_pmd_i40e   rte_pmd_vmxnet3_uio  rte_sched
	rte_cmdline  rte_ip_frag      rte_pipeline  rte_pmd_ixgbe rte_port       rte_table
)

SET(LUAJIT_LIBS
	luajit
)

SET(ALL_LIBS
	${LUAJIT_LIBS}
	-Wl,--start-group ${DPDK_LIBS} -Wl,--end-group
	pthread dl rt
)

INCLUDE_DIRECTORIES(
	${CMAKE_CURRENT_SOURCE_DIR}/deps/dpdk/x86_64-native-linuxapp-gcc/include
	${CMAKE_CURRENT_SOURCE_DIR}/deps/dpdk/drivers/net/ixgbe/base
	${CMAKE_CURRENT_SOURCE_DIR}/deps/dpdk/drivers/net/ixgbe
	${CMAKE_CURRENT_SOURCE_DIR}/deps/dpdk/drivers/net/i40e/base
	${CMAKE_CURRENT_SOURCE_DIR}/deps/dpdk/drivers/net/i40e
	${CMAKE_CURRENT_SOURCE_DIR}/deps/luajit/src
	${CMAKE_CURRENT_SOURCE_DIR}/lib
)

LINK_DIRECTORIES(
	${CMAKE_CURRENT_SOURCE_DIR}/deps/dpdk/x86_64-native-linuxapp-gcc/lib
	${CMAKE_CURRENT_SOURCE_DIR}/deps/luajit/usr/local/lib/
	${CMAKE_CURRENT_SOURCE_DIR}/deps/luajit/src
)

ADD_EXECUTABLE(${PRJ} ${FILES})
TARGET_LINK_LIBRARIES(${PRJ} ${ALL_LIBS})

