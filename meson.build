project('AWarp', 'cpp',
    default_options: ['buildtype=release', 'warning_level=2', 'b_lto=true', 'b_ndebug=if-release', 'cpp_std=c++17'],
    license: 'MIT',
    meson_version: '>=1.10.0',
    version: '2.0',
)

py = import('python').find_installation()
cxx = meson.get_compiler('cpp')

add_project_arguments(
    cxx.get_supported_arguments(['-fno-math-errno', '-fno-trapping-math']),
    language: 'cpp'
)

# Harcode the VS headers for now.
# Later they could be included in the VS wheel and discoverable via a function
vs_include = include_directories('include/vapoursynth')

# We install directly.
# meson-python usually treats this as 'data' in Pure mode (good for ABI),
# but we need the install_data step to fix the platform tag.
shared_module('awarp',
    'AWarp/awarp.cpp',
    include_directories: vs_include,
    gnu_symbol_visibility: 'hidden',
    name_prefix: '',
    install: true,
    install_dir: py.get_install_dir() / 'vapoursynth' / 'plugins'
)

# Force platform tag
# We install a dummy file into the root of the wheel data (not site-packages).
# The presence of a file in a non-pure path forces meson-python to give the wheel 
# a platform tag (win_amd64) without forcing the python ABI tag (e.g. cp314).
tag_file = configure_file(
    output: 'awarp.tag',
    configuration: configuration_data(),
)

install_data(
    tag_file,
    install_dir: get_option('libdir')
)