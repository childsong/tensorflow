# Description:
#   TensorFlow Serving session bundle.

load(
    "//tensorflow:tensorflow.bzl",
    "if_android",
    "if_ios",
    "if_mobile",
    "if_not_mobile",
    "py_test",
    "tf_cc_test",
)
load("//tensorflow/core:platform/default/build_config.bzl", "tf_proto_library")
# Placeholder for Google-internal load statements.

package(
    default_visibility = ["//visibility:public"],
    licenses = ["notice"],  # Apache 2.0
)

exports_files(["LICENSE"])

# TODO(b/32673259): add a test to continuously validate these files.
filegroup(
    name = "session_bundle_half_plus_two",
    srcs = glob([
        "testdata/half_plus_two/**",
        "testdata/half_plus_two_ckpt_v2/**",
    ]),
)

# Transitive dependencies of this target will be included in the pip package.
py_library(
    name = "session_bundle_pip",
    deps = [
        ":bundle_shim_py",
        ":exporter",
        ":gc",
    ],
)

py_library(
    name = "bundle_shim_py",
    srcs = ["bundle_shim.py"],
    srcs_version = "PY2AND3",
    visibility = ["//visibility:public"],
    deps = [
        ":constants",
        ":manifest_proto_py",
        ":session_bundle_py",
        "//tensorflow/core:protos_all_py",
        "//tensorflow/python:client",
        "//tensorflow/python:framework",
        "//tensorflow/python:platform",
        "//tensorflow/python:session",
        "//tensorflow/python/saved_model:constants",
        "//tensorflow/python/saved_model:loader",
        "//tensorflow/python/saved_model:signature_constants",
    ],
)

py_test(
    name = "bundle_shim_py_test",
    size = "small",
    srcs = ["bundle_shim_test.py"],
    data = [
        ":session_bundle_half_plus_two",
        "//tensorflow/cc/saved_model:saved_model_half_plus_two",
    ],
    main = "bundle_shim_test.py",
    python_version = "PY2",
    srcs_version = "PY2AND3",
    tags = ["no_pip"],
    deps = [
        ":bundle_shim_py",
        ":constants",
        ":manifest_proto_py",
        "//tensorflow/core:protos_all_py",
        "//tensorflow/python:client_testlib",
        "//tensorflow/python:framework",
        "//tensorflow/python:framework_for_generated_wrappers",
        "//tensorflow/python:parsing_ops",
        "//tensorflow/python:util",
        "//tensorflow/python/saved_model:signature_constants",
        "//tensorflow/python/saved_model:tag_constants",
    ],
)

# DEPRECATED: No longer supported. Switch to SavedModel immediately.
py_library(
    name = "constants",
    srcs = ["constants.py"],
    deprecation = "No longer supported. Switch to SavedModel immediately.",
    srcs_version = "PY2AND3",
)

# DEPRECATED: No longer supported. Switch to SavedModel immediately.
py_library(
    name = "exporter",
    srcs = ["exporter.py"],
    deprecation = "No longer supported. Switch to SavedModel immediately.",
    srcs_version = "PY2AND3",
    deps = [
        ":constants",
        ":gc",
        ":manifest_proto_py",
        "//tensorflow/core:protos_all_py",
        "//tensorflow/python:framework",
        "//tensorflow/python:framework_for_generated_wrappers",
        "//tensorflow/python:platform",
        "//tensorflow/python:training",
        "//tensorflow/python:util",
        "@six_archive//:six",
    ],
)

py_test(
    name = "exporter_test",
    size = "small",
    srcs = ["exporter_test.py"],
    python_version = "PY2",
    srcs_version = "PY2AND3",
    visibility = ["//visibility:private"],
    deps = [
        ":constants",
        ":exporter",
        ":gc",
        ":manifest_proto_py",
        "//tensorflow/core:protos_all_py",
        "//tensorflow/python:client_testlib",
        "//tensorflow/python:control_flow_ops",
        "//tensorflow/python:framework_for_generated_wrappers",
        "//tensorflow/python:math_ops",
        "//tensorflow/python:platform",
        "//tensorflow/python:session",
        "//tensorflow/python:state_ops",
        "//tensorflow/python:training",
        "//tensorflow/python:variables",
    ],
)

# DEPRECATED: No longer supported. Switch to SavedModel immediately.
py_library(
    name = "gc",
    srcs = ["gc.py"],
    deprecation = "No longer supported. Switch to SavedModel immediately.",
    srcs_version = "PY2AND3",
    deps = [
        "//tensorflow/python:framework",
        "//tensorflow/python:platform",
        "//tensorflow/python:util",
    ],
)

py_test(
    name = "gc_test",
    srcs = ["gc_test.py"],
    python_version = "PY2",
    srcs_version = "PY2AND3",
    tags = ["no_windows"],  # TODO: needs investigation on Windows
    visibility = ["//visibility:private"],
    deps = [
        ":gc",
        "//tensorflow/python:client_testlib",
        "//tensorflow/python:framework_test_lib",
        "//tensorflow/python:platform",
    ],
)

filegroup(
    name = "session_bundle_srcs",
    srcs = [
        "session_bundle.cc",
        "session_bundle.h",
    ],
)

filegroup(
    name = "signature_srcs",
    srcs = [
        "signature.cc",
        "signature.h",
    ],
)

# DEPRECATED: No longer supported. Switch to SavedModel immediately.
cc_library(
    name = "session_bundle",
    hdrs = ["session_bundle.h"],
    deprecation = "No longer supported. Switch to SavedModel immediately.",
    visibility = ["//visibility:public"],
    deps = [
        ":manifest_proto_cc",
        ":session_bundle_lite",
        ":signature",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_cc",
    ],
)

# DEPRECATED: No longer supported. Switch to SavedModel immediately.
# This is a lite version of the session_bundle target that does not link in any
# Tensorflow ops in order to minimize its size. Clients using this should link
# any required ops manually.
cc_library(
    name = "session_bundle_lite",
    srcs = ["session_bundle.cc"],
    hdrs = ["session_bundle.h"],
    copts = if_ios(["-DGOOGLE_LOGGING"]),
    deprecation = "No longer supported. Switch to SavedModel immediately.",
    visibility = ["//visibility:public"],
    deps = [
        ":signature_lite",
        "//tensorflow/core:lib_internal",
    ] + if_not_mobile([
        ":manifest_proto_cc",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core/util/tensor_bundle:naming",
        # mobile not supported yet
    ]),
)

tf_cc_test(
    name = "session_bundle_test",
    size = "medium",
    srcs = ["session_bundle_test.cc"],
    data = [":session_bundle_half_plus_two"],
    # Link in all registered kernels.
    linkstatic = 1,
    visibility = ["//visibility:private"],
    deps = [
        ":session_bundle",
        ":signature",
        ":test_util",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/core:testlib",
    ],
)

# DEPRECATED: No longer supported. Switch to SavedModel immediately.
py_library(
    name = "session_bundle_py",
    srcs = ["session_bundle.py"],
    deprecation = "No longer supported. Switch to SavedModel immediately.",
    srcs_version = "PY2AND3",
    visibility = ["//visibility:public"],
    deps = [
        ":constants",
        ":manifest_proto_py",
        "//tensorflow/core:protos_all_py",
        "//tensorflow/python:client",
        "//tensorflow/python:framework_for_generated_wrappers",
        "//tensorflow/python:lib",
        "//tensorflow/python:session",
        "//tensorflow/python:training",
        "//tensorflow/python:util",
    ],
)

py_test(
    name = "session_bundle_py_test",
    size = "small",
    srcs = ["session_bundle_test.py"],
    data = [":session_bundle_half_plus_two"],
    main = "session_bundle_test.py",
    python_version = "PY2",
    srcs_version = "PY2AND3",
    tags = ["no_pip"],
    deps = [
        ":constants",
        ":manifest_proto_py",
        ":session_bundle_py",
        "//tensorflow/core:protos_all_py",
        "//tensorflow/python:array_ops",
        "//tensorflow/python:client_testlib",
        "//tensorflow/python:framework_for_generated_wrappers",
        "//tensorflow/python:graph_util",
        "//tensorflow/python:math_ops",
        "//tensorflow/python:parsing_ops",
        "//tensorflow/python:training",
        "//tensorflow/python:util",
        "//tensorflow/python:variables",
        "//third_party/py/numpy",
    ],
)

# DEPRECATED: No longer supported. Switch to SavedModel immediately.
# This is a lite version of the signature target that does not link in any
# Tensorflow ops in order to minimize its size. Clients using this should
# link any required ops manually.
cc_library(
    name = "signature_lite",
    srcs = ["signature.cc"],
    hdrs = ["signature.h"],
    deprecation = "No longer supported. Switch to SavedModel immediately.",
    visibility = ["//visibility:public"],
    deps = [
    ] + if_not_mobile([
        ":manifest_proto_cc",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:lib_internal",
        "//tensorflow/core:protos_all_cc",
        # mobile not supported yet
    ]),
)

# DEPRECATED: No longer supported. Switch to SavedModel immediately.
cc_library(
    name = "signature",
    hdrs = ["signature.h"],
    deprecation = "No longer supported. Switch to SavedModel immediately.",
    visibility = ["//visibility:public"],
    deps = [
        ":manifest_proto_cc",
        ":signature_lite",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_cc",
    ] + if_not_mobile([
        "//tensorflow/core:tensorflow_opensource",
    ]),
)

tf_cc_test(
    name = "signature_test",
    size = "small",
    srcs = ["signature_test.cc"],
    visibility = ["//visibility:private"],
    deps = [
        ":manifest_proto_cc",
        ":signature",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core:tensorflow_opensource",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/core:testlib",
    ],
)

# DEPRECATED: No longer supported. Switch to SavedModel immediately.
cc_library(
    name = "test_util",
    testonly = 1,
    srcs = ["test_util.cc"],
    hdrs = ["test_util.h"],
    deprecation = "No longer supported. Switch to SavedModel immediately.",
    visibility = ["//visibility:private"],
    deps = [
        "//tensorflow/core:lib",
        "//tensorflow/core:test",
    ],
)

cc_library(
    name = "bundle_shim",
    srcs = ["bundle_shim.cc"],
    hdrs = ["bundle_shim.h"],
    copts = if_ios(["-DGOOGLE_LOGGING"]),
    visibility = ["//visibility:public"],
    deps = [
        ":session_bundle",
        ":signature",
        "//tensorflow/cc/saved_model:loader",
        "//tensorflow/cc/saved_model:signature_constants",
    ] + if_not_mobile([
        ":manifest_proto_cc",
        "//tensorflow/core:core_cpu",
        "//tensorflow/core:framework",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_cc",
        # mobile not supported yet
    ]),
)

tf_cc_test(
    name = "bundle_shim_test",
    size = "small",
    srcs = ["bundle_shim_test.cc"],
    data = [
        ":session_bundle_half_plus_two",
        "//tensorflow/cc/saved_model:saved_model_half_plus_two",
    ],
    # Link in all registered kernels.
    linkstatic = 1,
    deps = [
        ":bundle_shim",
        ":test_util",
        "//tensorflow/cc/saved_model:signature_constants",
        "//tensorflow/cc/saved_model:tag_constants",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/core:testlib",
    ],
)

# DEPRECATED: No longer supported. Switch to SavedModel immediately.
tf_proto_library(
    name = "manifest_proto",
    srcs = ["manifest.proto"],
    cc_api_version = 2,
    visibility = ["//visibility:public"],
)
