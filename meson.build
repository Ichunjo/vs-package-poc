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


shared_module('awarp',
    files('AWarp/awarp.cpp'),
    include_directories: vs_include,
    gnu_symbol_visibility: 'hidden',
    install: true,
    install_dir: py.get_install_dir() / 'vapoursynth' / 'plugins',
    name_prefix: '',
)
