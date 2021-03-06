package(default_visibility = ["//visibility:public"])

# Test data.
filegroup(
    name = "testdata",
    data = glob(["testdata/**"]),
)

cc_library(
    name = "beam",
    hdrs = ["beam.h"],
    deps = [
        "//dragnn/core/interfaces:cloneable_transition_state",
        "//dragnn/core/interfaces:transition_state",
        "@org_tensorflow//tensorflow/core:lib",  # For tf/core/platform/logging.h
    ],
)

cc_library(
    name = "component_registry",
    srcs = ["component_registry.cc"],
    hdrs = ["component_registry.h"],
    deps = [
        "//dragnn/core/interfaces:component",
        "//syntaxnet:registry",
    ],
)

cc_library(
    name = "compute_session",
    hdrs = ["compute_session.h"],
    deps = [
        "//dragnn/components/util:bulk_feature_extractor",
        "//dragnn/core:index_translator",
        "//dragnn/core/interfaces:component",
        "//dragnn/protos:spec_proto",
        "//dragnn/protos:trace_proto",
    ],
)

cc_library(
    name = "compute_session_impl",
    srcs = ["compute_session_impl.cc"],
    hdrs = ["compute_session_impl.h"],
    deps = [
        ":compute_session",
        ":index_translator",
        ":input_batch_cache",
        "//dragnn/components/util:bulk_feature_extractor",
        "//dragnn/protos:data_proto",
        "//dragnn/protos:spec_proto",
        "//dragnn/protos:trace_proto",
        "//syntaxnet:registry",
        "@org_tensorflow//tensorflow/core:lib",  # For tf/core/platform/logging.h
    ],
)

cc_library(
    name = "compute_session_pool",
    srcs = ["compute_session_pool.cc"],
    hdrs = ["compute_session_pool.h"],
    deps = [
        ":component_registry",
        ":compute_session",
        ":compute_session_impl",
        "//dragnn/protos:spec_proto",
        "@org_tensorflow//tensorflow/core:lib",
    ],
)

cc_library(
    name = "index_translator",
    srcs = ["index_translator.cc"],
    hdrs = ["index_translator.h"],
    deps = [
        "//dragnn/core/interfaces:component",
        "//dragnn/core/interfaces:transition_state",
        "@org_tensorflow//tensorflow/core:lib",  # For tf/core/platform/logging.h
    ],
)

cc_library(
    name = "input_batch_cache",
    hdrs = ["input_batch_cache.h"],
    deps = [
        "//dragnn/core/interfaces:input_batch",
        "@org_tensorflow//tensorflow/core:lib",  # For tf/core/platform/logging.h
    ],
)

cc_library(
    name = "resource_container",
    hdrs = ["resource_container.h"],
    deps = [
        "//syntaxnet:base",
        "@org_tensorflow//tensorflow/core:framework",
    ],
)

# Tests

cc_test(
    name = "beam_test",
    srcs = ["beam_test.cc"],
    deps = [
        ":beam",
        "//dragnn/core/interfaces:cloneable_transition_state",
        "//dragnn/core/interfaces:transition_state",
        "//dragnn/core/test:mock_transition_state",
        "//syntaxnet:test_main",
        "@org_tensorflow//tensorflow/core:test",
    ],
)

cc_test(
    name = "compute_session_impl_test",
    srcs = ["compute_session_impl_test.cc"],
    deps = [
        ":component_registry",
        ":compute_session",
        ":compute_session_impl",
        ":compute_session_pool",
        "//dragnn/components/util:bulk_feature_extractor",
        "//dragnn/core/interfaces:component",
        "//dragnn/core/test:generic",
        "//dragnn/core/test:mock_component",
        "//dragnn/core/test:mock_transition_state",
        "@org_tensorflow//tensorflow/core:test",
    ],
)

cc_test(
    name = "compute_session_pool_test",
    srcs = ["compute_session_pool_test.cc"],
    deps = [
        ":compute_session",
        ":compute_session_pool",
        "//dragnn/core/test:generic",
        "//dragnn/core/test:mock_component",
        "//dragnn/core/test:mock_compute_session",
        "//syntaxnet:test_main",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:test",
    ],
)

cc_test(
    name = "index_translator_test",
    srcs = ["index_translator_test.cc"],
    deps = [
        ":index_translator",
        "//dragnn/core/test:mock_component",
        "//dragnn/core/test:mock_transition_state",
        "//syntaxnet:test_main",
        "@org_tensorflow//tensorflow/core:test",
    ],
)

cc_test(
    name = "input_batch_cache_test",
    srcs = ["input_batch_cache_test.cc"],
    deps = [
        ":input_batch_cache",
        "//dragnn/core/interfaces:input_batch",
        "//syntaxnet:test_main",
        "@org_tensorflow//tensorflow/core:test",
    ],
)

cc_test(
    name = "resource_container_test",
    srcs = ["resource_container_test.cc"],
    deps = [
        ":resource_container",
        "//syntaxnet:test_main",
        "@org_tensorflow//tensorflow/core:test",
    ],
)

# Tensorflow op kernel BUILD rules.

load(
    "//dragnn:tensorflow_ops.bzl",
    "tf_gen_op_libs",
    "tf_gen_op_wrapper_py",
    "tf_kernel_library",
)

tf_gen_op_libs(
    op_lib_names = ["dragnn_ops"],
)

tf_gen_op_wrapper_py(
    name = "dragnn_ops",
    deps = [":dragnn_ops_op_lib"],
)

tf_gen_op_libs(
    op_lib_names = ["dragnn_bulk_ops"],
)

tf_gen_op_wrapper_py(
    name = "dragnn_bulk_ops",
    deps = [":dragnn_bulk_ops_op_lib"],
)

cc_library(
    name = "compute_session_op",
    srcs = [
        "ops/compute_session_op.cc",
    ],
    hdrs = ["ops/compute_session_op.h"],
    deps = [
        ":compute_session",
        ":resource_container",
        "@org_tensorflow//tensorflow/core:framework",
        "@org_tensorflow//third_party/eigen3",
    ],
)

cc_library(
    name = "dragnn_ops_cc",
    srcs = [
        "ops/dragnn_op_kernels.cc",
        "ops/dragnn_ops.cc",
    ],
    deps = [
        ":compute_session",
        ":compute_session_op",
        ":compute_session_pool",
        ":resource_container",
        "//dragnn/protos:data_proto",
        "//dragnn/protos:spec_proto",
        "@org_tensorflow//tensorflow/core:framework",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//third_party/eigen3",
    ],
    alwayslink = 1,
)

cc_library(
    name = "dragnn_bulk_ops_cc",
    srcs = [
        "ops/dragnn_bulk_op_kernels.cc",
        "ops/dragnn_bulk_ops.cc",
    ],
    deps = [
        ":compute_session_op",
        ":resource_container",
        "@org_tensorflow//tensorflow/core:framework",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
        "@org_tensorflow//third_party/eigen3",
    ],
)

# Tensorflow kernel libraries, for use with unit tests.

tf_kernel_library(
    name = "dragnn_op_kernels",
    srcs = [
        "ops/dragnn_op_kernels.cc",
        "ops/dragnn_ops.cc",
    ],
    hdrs = [
    ],
    deps = [
        ":compute_session",
        ":compute_session_op",
        ":compute_session_pool",
        ":resource_container",
        "//dragnn/protos:data_proto",
        "//dragnn/protos:spec_proto",
        "@org_tensorflow//tensorflow/core:framework",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//third_party/eigen3",
    ],
)

tf_kernel_library(
    name = "dragnn_bulk_op_kernels",
    srcs = [
        "ops/dragnn_bulk_op_kernels.cc",
        "ops/dragnn_bulk_ops.cc",
    ],
    hdrs = [
    ],
    deps = [
        ":compute_session",
        ":compute_session_op",
        ":compute_session_pool",
        ":resource_container",
        "//dragnn/components/util:bulk_feature_extractor",
        "//dragnn/protos:spec_proto",
        "@org_tensorflow//tensorflow/core:framework",
        "@org_tensorflow//tensorflow/core:lib",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
        "@org_tensorflow//third_party/eigen3",
    ],
)

# Tensorflow kernel tests.

cc_test(
    name = "dragnn_op_kernels_test",
    srcs = ["ops/dragnn_op_kernels_test.cc"],
    deps = [
        ":compute_session",
        ":compute_session_pool",
        ":dragnn_op_kernels",
        ":resource_container",
        "//dragnn/core/test:generic",
        "//dragnn/core/test:mock_compute_session",
        "//syntaxnet:test_main",
        "@org_tensorflow//tensorflow/core:framework",
        "@org_tensorflow//tensorflow/core:protos_all_cc",
        "@org_tensorflow//tensorflow/core:test",
        "@org_tensorflow//tensorflow/core:testlib",
        "@org_tensorflow//tensorflow/core/kernels:ops_testutil",
        "@org_tensorflow//tensorflow/core/kernels:ops_util",
        "@org_tensorflow//tensorflow/core/kernels:quantized_ops",
    ],
)

cc_test(
    name = "dragnn_bulk_op_kernels_test",
    srcs = ["ops/dragnn_bulk_op_kernels_test.cc"],
    deps = [
        ":compute_session_pool",
        ":dragnn_bulk_op_kernels",
        ":resource_container",
        "//dragnn/components/util:bulk_feature_extractor",
        "//dragnn/core/test:mock_compute_session",
        "//syntaxnet:test_main",
        "@org_tensorflow//tensorflow/core:framework",
        "@org_tensorflow//tensorflow/core:testlib",
        "@org_tensorflow//tensorflow/core/kernels:ops_testutil",
        "@org_tensorflow//tensorflow/core/kernels:quantized_ops",
    ],
)
