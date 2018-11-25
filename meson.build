project('rift-playground', 'c',
  version : '1.0.0',
  meson_version: '>= 0.37.0',
  license : 'GPL3+',
   default_options: [
    'c_std=gnu11',
    'cpp_std=c++11',
    'warning_level=2',
    ],
)

version_split = meson.project_version().split('.')
MAJOR_VERSION = version_split[0]
MINOR_VERSION = version_split[1]
MICRO_VERSION = version_split[2]


# Basic config stuff
conf = configuration_data()
conf.set_quoted('PACKAGE_NAME', 'rift-playground')
conf.set_quoted('PACKAGE_VERSION', meson.project_version())
conf.set_quoted('PACKAGE_STRING', 'rift-playground-' + meson.project_version())
conf.set_quoted('PACKAGE_DATADIR', join_paths(get_option('prefix'), get_option('datadir')))
conf.set_quoted('PACKAGE_DOCDIR', join_paths(get_option('prefix'), get_option('datadir'), 'doc/rift-playground'))
conf.set_quoted('PACKAGE_LIBDIR', join_paths(get_option('prefix'), get_option('libdir')))
conf.set_quoted('PACKAGE_LOCALE_DIR', join_paths(get_option('prefix'), get_option('datadir'), 'locale'))
conf.set_quoted('PACKAGE_LIBEXECDIR', join_paths(get_option('prefix'), get_option('libexecdir')))

conf.set('GETTEXT_PACKAGE', 'PACKAGE_NAME')
conf.set('LOCALEDIR', 'PACKAGE_LOCALE_DIR')

conf.set10('ENABLE_NLS', true) # Always enabled

# We should probably avoid using these
conf.set_quoted('SRCDIR', meson.source_root())
conf.set_quoted('BUILDDIR', meson.build_root())

# Features to enable

if get_option('enable_tracing')
	conf.set('DEBUG_TRACE_LEVEL', true)
endif

# Define directories
vapi_dir = join_paths(meson.current_source_dir(), 'vapi')
source_dir = meson.current_source_dir()


add_global_arguments([
  '-DHAVE_CONFIG_H',
  '-I' + meson.build_root(), # config.h
  '-D_GNU_SOURCE',
], language: 'c')

cc = meson.get_compiler('c')
global_c_args = [
  '-Werror=format-nonliteral',
]
test_c_args = [
  '-Wcast-align',
  '-Wdeclaration-after-statement',
  ['-Werror=format-security', '-Werror=format=2'],
  '-Wformat-nonliteral',
  '-Wformat-security',
  '-Wmissing-include-dirs',
  '-Wnested-externs',
  '-Wno-missing-field-initializers',
  '-Wno-sign-compare',
  '-Wno-strict-aliasing',
  '-Wno-uninitialized',
  '-Wno-unused-parameter',
  '-Wpointer-arith',
  '-Wredundant-decls',
  '-Wshadow',
  '-Wswitch-default',
  '-Wswitch-enum',
  '-Wundef',
]

if get_option('buildtype') != 'plain'
  test_c_args += '-fstack-protector-strong'
endif

if get_option('enable_profiling')
  global_c_args +=['-g', '-pg']
endif


foreach arg: test_c_args
  if cc.has_multi_arguments(arg)
    global_c_args += arg
  endif
endforeach
add_project_arguments(
  global_c_args,
  language: 'c'
)


global_link_args = []
test_link_args = [
  '-Wl,-z,relro',
  '-Wl,-z,now',
]

# More strict build options
if not get_option('buildtype').startswith('debug')

  # TODO: Maybe reuse 'b_ndebug' option
  add_global_arguments([
    '-DG_DISABLE_CAST_CHECKS',
    '-DG_DISABLE_ASSERT',
    '-DG_DISABLE_CHECKS',
  ], language: 'c')

  test_link_args += [
    '-Wl,-Bsymbolic',
    '-fno-plt',
  ]
else
  add_global_arguments([
    '-g',
  ], language: 'c')
endif

foreach arg: test_link_args
  if cc.has_argument(arg)
    global_link_args += arg
  endif
endforeach
add_project_link_arguments(
  global_link_args,
  language: 'c'
)

# FIXME Not needed by us
#if cc.has_function('sched_getcpu')
#  conf.set('HAVE_SCHED_GETCPU', true)
#endif





# Modules Required
libsdl2_dep = dependency('sdl2', version: '>=2.0.8')
libusb_dep = dependency('libusb-1.0', version: '>=1.0.0')
libuvc_dep = dependency('libuvc', version: '>=0.0.6')


#libglib_dep = dependency('glib-2.0')
#libgobject_dep = dependency('gobject-2.0')

# Comms Modules Required

# C libraries
#liblog4c_dep = cc.find_library('log4c', required: false)

cpu = host_machine.cpu_family()
if cpu == 'arm'
#	project_libdirs = ['/opt/VAL100/buildroot/buildroot-2015.05/output/host/usr/arm-buildroot-linux-gnueabihf/sysroot/usr/lib']
	conf.set('HAVE_ARM_SYSTEM', true)
	# Libraries to be found with the compiler
	# FIXME use version info. At least 1.7.8
	#libgcrypt_dep = cc.find_library('libgcrypt', required: true, directories : project_libdirs)
#	libgcrypt_dep = cc.find_library('libgcrypt', required: true, dirs : project_libdirs)
else
#	libgcrypt_dep = cc.find_library('libgcrypt', required: true)
endif

#if libgcrypt_dep.found()
#	conf.set('HAVE_GCRYPT', true)
#endif



configure_file(
  output: 'config.h',
  configuration: conf
)


# Build programs
#maven_prog = find_program('mvn', required : false)
#valabind_prog = find_program('valabind', required : false)
#swig_prog = find_program('swig', required : false)
true_prog = find_program('true')
copy_prog = find_program('cp')
#java_prog = find_program('javac', required: false)


#if java_prog.found()
#	java_build_include = include_directories([
#		'/usr/lib/jvm/default-java/include/',
#		'/usr/lib/jvm/default-java/include/linux/',
#	])
##	java_build_include = '/usr/lib/jvm/default-java/include/'
## FIXME does not work
##	if not cc.has_header('jni.h', prefix: java_build_include)
##		error('Cannot find <jni.h>')
##	endif
#endif

# Define some paths
#pkglibdir_abs = join_paths(get_option('prefix'), get_option('libdir'), 'gnome-builder')
pkglibdir = join_paths(get_option('libdir'), 'rift-playgrounds')
pkggirdir = join_paths(get_option('datadir'),'rift-playgrounds', 'gir-1.0')
pkgtypelibdir = join_paths(get_option('libdir'), 'rift-playgrounds', 'girepository-1.0')
pkgvapidir = join_paths(get_option('datadir'), 'rift-playgrounds/vapi')

#gnome = import('gnome')

# Not portable
#system_include_dirs = [
#	'/usr/lib/jvm/java-7-openjdk-amd64/include/linux',
#	'/usr/lib/jvm/java-7-openjdk-amd64/include/',
#	'/usr/lib/x86_64-linux-gnu/glib-2.0/include',
#	'/usr/include/glib-2.0',
#	'.',
#]



#subdir('src')
#subdir('vala')
#subdir('tests')

#if maven_prog.found()
#  subdir('java2vala')
#endif



exe_link_args = [
  '-pie',
]
exe_c_args = [
  '-fPIE',
]

#pkg_mod = import('pkgconfig')
#pkg_mod.generate(libraries : sg64_core_library,
#								 subdirs : [sg64_core_header_subdir],
#                 version : meson.project_version(),
#                 name : 'librift-playgrounds',
#                 filebase : 'rift-playgrounds',
#                 description : 'Core libraries for sg64 system.',
#                  )
#libraries : rift-playgrounds_vala_deps



### Build the thing

rift_playground_sources = [
		'src/blobwatch.h','src/blobwatch.c',
		'src/main.c'
]

rift_playground_deps = [
	libsdl2_dep,
	libusb_dep,
	libuvc_dep,
]
rift_playground_source_dir = [join_paths('src')]


rift_playground = executable('rift-playground',
			rift_playground_sources,
			dependencies: rift_playground_deps ,
#     	link_with : sg64_core_library,
     	include_directories : include_directories(rift_playground_source_dir),
			)

