include_directories(.)

set(PROX_SRCS
  prox/prox.h
  prox/proxdef.h
  prox/knapsack_eq.h
  prox/knapsack_le.h
  prox/knapsack_le_biased.h
  prox/topk_cone.h
  prox/topk_cone_biased.h
  prox/topk_simplex.h
  prox/topk_simplex_biased.h
  )

set(PLAY_SRCS
  play/main.cpp
  )

# Matlab interface
if(${Matlab_FOUND})
  include_directories(matlab)
  include_directories(${Matlab_INCLUDE_DIRS})

  set(MEX_PROX libsdca_prox)
  add_definitions(-DMEX_PROX="${MEX_PROX}")

  matlab_add_mex(
    NAME ${MEX_PROX}
    SRC matlab/mex_prox.cpp matlab/mex_util.h ${PROX_SRCS}
    )

  set_target_properties(${MEX_PROX}
    PROPERTIES COMPILE_FLAGS "-Wno-unused-parameter"
    )

  install(
    TARGETS ${MEX_PROX}
    DESTINATION matlab
    )
else()
  message(STATUS "Matlab interface will not be compiled.")
endif()

# A playground for quick checks and small tests
add_executable(main ${PLAY_SRCS} ${PROX_SRCS})
target_link_libraries(main ${BLAS_LIBRARIES})