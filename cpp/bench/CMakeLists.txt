#=============================================================================
# Copyright (c) 2019-2020, NVIDIA CORPORATION.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#=============================================================================

cmake_policy(SET CMP0079 NEW)

##############################################################################
# - build cuml bench executable ----------------------------------------------

if(BUILD_CUML_BENCH)
  # (please keep the filenames in alphabetical order)
  add_executable(sg_benchmark
    sg/arima_loglikelihood.cu
    sg/dbscan.cu
    sg/kmeans.cu
    sg/main.cpp
    sg/rf_classifier.cu
    # FIXME: RF Regressor is having an issue where the tests now seem to take
    # forever to finish, as opposed to the classifier counterparts!
    # sg/rf_regressor.cu
    sg/svc.cu
    sg/svr.cu
    sg/umap.cu
    sg/fil.cu
    )

  target_link_libraries(sg_benchmark
    ${CUML_CPP_TARGET}
    benchmarklib)

  target_include_directories(sg_benchmark
    PRIVATE ${GBENCH_DIR}/include
    ${CUML_INCLUDE_DIRECTORIES})
endif(BUILD_CUML_BENCH)

##############################################################################
# - build prims bench executable ----------------------------------------------

if(BUILD_CUML_PRIMS_BENCH)
  # (please keep the filenames in alphabetical order)
  add_executable(prims_benchmark
    prims/add.cu
    prims/distance_cosine.cu
    prims/distance_exp_l2.cu
    prims/distance_l1.cu
    prims/distance_unexp_l2.cu
    prims/fused_l2_nn.cu
    prims/gram_matrix.cu
    prims/main.cpp
    prims/make_blobs.cu
    prims/map_then_reduce.cu
    prims/matrix_vector_op.cu
    prims/permute.cu
    prims/reduce.cu
    prims/rng.cu
    ../src/common/logger.cpp)  # because prims is header only!

  if(NOT CUB_IS_PART_OF_CTK)
    add_dependencies(prims_benchmark cub)
  endif(NOT CUB_IS_PART_OF_CTK)
  add_dependencies(prims_benchmark spdlog)

  target_link_libraries(prims_benchmark ${CUDA_cublas_LIBRARY} benchmarklib)

  target_include_directories(prims_benchmark
    PRIVATE ${GBENCH_DIR}/include
    ${CUML_INCLUDE_DIRECTORIES})
endif(BUILD_CUML_PRIMS_BENCH)
