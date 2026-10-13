project('bx', 'cpp')

add_project_arguments('-funsigned-char', language: 'cpp')

if host_machine.system() == 'Linux':
  add_project_arguments('-fPIC', language: 'cpp')
endif


fs = import('fs')
sources = []
foreach s : fs.read('sources.txt').strip().split('\n')
    sources += files('src/' + s + '.cpp')
endforeach

if host_machine.system() == 'darwin'
    compat_dir = 'include/compat/osx'
elif host_machine.system() == 'windows'
    compat_dir = 'include/compat/msvc'
else
    compat_dir = 'include/compat/linux'
endif

bx_inc = include_directories('include', compat_dir)

bx_cpp_args = []

if (get_option('buildtype') == 'debug')
    bx_cpp_args += ['-DBX_CONFIG_DEBUG=1']
else
    bx_cpp_args += ['-DBX_CONFIG_DEBUG=0']
endif

cc = meson.get_compiler('cpp')
if cc.get_id() == 'msvc' or cc.get_id() == 'clang-cl'
    # /Zc:preprocessor — required by bgfx's platform.h on modern MSVC.
    # /EHa — allow SEH (__try/__except) in functions with C++ objects
    # (bx/src/thread.cpp uses __try alongside std::string-like types).
    bx_cpp_args += ['/Zc:preprocessor', '/EHa']
endif

bx_lib = static_library(
    'bx',
    sources,
    include_directories: bx_inc,
    cpp_args: bx_cpp_args,
)

bx_dep = declare_dependency(
    include_directories: bx_inc,
    link_with: bx_lib,
    compile_args: bx_cpp_args,
)

meson.override_dependency('bx', bx_dep)
