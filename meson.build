project('tree-sitter-yaml', 'c',
  version: '0.7.2',
  default_options: ['c_std=gnu11', 'optimization=3']
)

yaml_schema = get_option('yaml_schema') # 'core', 'json', ou 'legacy'
add_project_arguments('-DYAML_SCHEMA=' + yaml_schema, language: 'c')

## Generate parser.c with tree-sitter CLI if available
ts_cli = find_program('tree-sitter', required: false)
if ts_cli.found()
  abi = get_option('tree_sitter_abi')
  parser_c = custom_target('parser_gen',
    input: 'src/grammar.json',
    output: 'parser.c',
    command: [ts_cli, 'generate', '--abi=' + abi.to_string(), '@INPUT@', '--output', '@OUTDIR@'],
    install: false
  )
else
  parser_c = files('src/parser.c')
endif

src = [parser_c, 'src/scanner.c']
inc = include_directories('src', 'bindings/c')

## Library
tree_sitter_yaml_lib = library('tree-sitter-yaml',
  src,
  include_directories: inc,
  install: true,
  soversion: '14',
  version: meson.project_version()
)

tree_sitter_yaml_dep = declare_dependency(
  include_directories: inc,
  link_with: tree_sitter_yaml_lib
)

meson.override_dependency('tree-sitter-yaml', tree_sitter_yaml_dep)

## PKGCONFIG
pkg = import('pkgconfig')
pkg.generate(tree_sitter_yaml_lib,
  name: 'tree-sitter-yaml',
  description: 'YAML grammar for tree-sitter',
)

## Install
install_subdir('bindings/c/tree_sitter', install_dir: get_option('includedir'))
install_subdir('queries', install_dir: get_option('datadir') / 'tree-sitter/queries/yaml', strip_directory: true)
