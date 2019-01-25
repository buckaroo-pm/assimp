load('//:buckaroo_macros.bzl', 'buckaroo_deps')
load('//:subdir_glob.bzl', 'subdir_glob')


genrule(
  name = 'config.h',
  out = 'config.h',
  srcs = ['include/assimp/config.h.in'],
  cmd = 'cat $SRCS | sed -e \'s/#cmake/#/\' > $OUT',
)


genrule(
  name = 'revision.h',
  out = 'revision.h',
  cmd = 'echo "#ifndef ASSIMP_REVISION_H_INC \n' +
  '#define ASSIMP_REVISION_H_INC \n' +
  ' \n' +
  '#define GitVersion 0x0 \n' +
  '#define GitBranch \\"buckaroo-managed-dep\\" \n' +
  ' \n' +
  '#endif // ASSIMP_REVISION_H_INC\n" > $OUT',
)

windows_sources = glob([
  'code/C4DImporter.cpp',
])

platform_sources = windows_sources

macos_flags = [
  '-DASSIMP_BUILD_NO_OWN_ZLIB',
#  '-DASSIMP_BUILD_NO_C4D_IMPORTER',
]

linux_flags = [
  '-DASSIMP_BUILD_NO_OWN_ZLIB',
#  '-DASSIMP_BUILD_NO_C4D_IMPORTER',
]

prebuilt_cxx_library(
  name = 'assimp-headers',
  header_only = True,
  header_namespace = '',
  exported_headers = subdir_glob([
    ('code', '**/*.h'),
  ]),
)

cxx_library(
  name = 'assimp',
  header_namespace = '',
  exported_headers = dict(
    subdir_glob([
      ('include', 'assimp/**/*.hpp'),
      ('include', 'assimp/**/*.h'),
      ('include', 'assimp/**/*.inl'),
    ]).items() + [
      ('revision.h', ':revision.h'),
      ('assimp/config.h', ':config.h')
    ]
  ),
  headers = subdir_glob([
    ('', 'code/**/*.h'),
    ('', 'code/**/*.hpp'),
  ]),
  srcs = glob([
    'code/**/*.cpp',
    'contrib/irrXML/**/*.cpp',
  ], exclude = platform_sources),
  platform_srcs = [
    ('^windows.*', windows_sources),
  ],
  platform_compiler_flags = [
    ('default', linux_flags),
    ('^macos.*', macos_flags),
    ('^linux.*', linux_flags),
  ],
  visibility = [
    'PUBLIC',
  ],
  deps = buckaroo_deps() + [
    ':assimp-headers'
  ],
)
