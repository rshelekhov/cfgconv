# Config File Converter - Development Plan in Gleam

## Project Description
CLI tool for converting configuration files between YAML, JSON, TOML formats with structure and data type validation.

## Project Architecture
```
src/
├── main.gleam              # Entry point
├── cli/
│   ├── args.gleam         # Command line argument parsing
│   ├── commands.gleam     # CLI commands
│   └── output.gleam       # Output formatting
├── parsers/
│   ├── json.gleam         # JSON parser/serializer
│   ├── yaml.gleam         # YAML parser/serializer
│   └── toml.gleam         # TOML parser/serializer
├── validation/
│   ├── schema.gleam       # Schema definitions
│   ├── validator.gleam    # Data validation
│   └── rules.gleam        # Validation rules
└── core/
    ├── types.gleam        # Common data types
    ├── converter.gleam    # Main conversion logic
    └── error.gleam        # Error handling
```

## Development Stages

### Stage 1: Project Setup and Basic Structure (1-2 days)
**Tasks:**
- [ ] `gleam new config_converter` - create project
- [ ] Setup `gleam.toml` with dependencies
- [ ] Create basic folder structure
- [ ] Define core types in `core/types.gleam`

**Dependencies to add:**
```toml
[dependencies]
gleam_stdlib = "~> 0.34"
gleam_json = "~> 1.0"
argv = "~> 1.0"
filepath = "~> 1.0"
simplifile = "~> 1.0"
```

**Core types:**
```gleam
pub type ConfigFormat {
  Json
  Yaml  
  Toml
}

pub type ConfigData {
  ConfigData(content: String, format: ConfigFormat)
}

pub type ConvertError {
  ParseError(String)
  ValidationError(String)
  FileError(String)
  UnsupportedFormat(String)
}
```

### Stage 2: JSON Parser and Basic CLI (2-3 days)
**Tasks:**
- [ ] Implement `parsers/json.gleam` with parsing and serialization
- [ ] Basic command line argument parsing in `cli/args.gleam`
- [ ] Entry point in `main.gleam` with `convert` command handling
- [ ] Simple JSON → JSON conversion (for testing pipeline)

**CLI interface:**
```bash
./config_converter convert --input config.json --output config.json --format json
./config_converter convert -i config.json -o result.yaml -f yaml
```

**Testing:**
- [ ] Create test JSON files
- [ ] Unit tests for parser
- [ ] E2E test for convert command

### Stage 3: YAML Support (2-3 days)
**Tasks:**
- [ ] Research YAML libraries for Erlang/Gleam
- [ ] Implement `parsers/yaml.gleam`
- [ ] Add YAML to `core/converter.gleam`
- [ ] JSON ↔ YAML conversion

**Notes:**
- May need to use Erlang libraries through FFI
- Handle YAML specifics (multiline strings, comments)

**Testing:**
- [ ] YAML files with various structures
- [ ] JSON → YAML → JSON roundtrip tests

### Stage 4: TOML Support (2-3 days)
**Tasks:**
- [ ] Research TOML libraries
- [ ] Implement `parsers/toml.gleam`
- [ ] Complete conversion matrix: JSON ↔ YAML ↔ TOML
- [ ] Handle TOML-specific types (datetime, arrays of tables)

**Testing:**
- [ ] TOML configurations of varying complexity
- [ ] Roundtrip tests for all formats

### Stage 5: Basic Validation (3-4 days)
**Tasks:**
- [ ] Define validation schemas in `validation/schema.gleam`
- [ ] Implement basic rules in `validation/rules.gleam`
- [ ] Validator in `validation/validator.gleam`
- [ ] CLI `validate` command

**Validation schemas:**
```gleam
pub type SchemaRule {
  Required(key: String)
  TypeCheck(key: String, expected_type: ConfigType)
  Range(key: String, min: Int, max: Int)
  Pattern(key: String, regex: String)
}

pub type ConfigType {
  StringType
  IntType
  BoolType
  ArrayType(ConfigType)
  ObjectType
}
```

**CLI:**
```bash
./config_converter validate --input config.yaml --schema schema.json
```

### Stage 6: Enhanced Error Handling (1-2 days)
**Tasks:**
- [ ] Detailed error messages with line/column information
- [ ] Colored error output in terminal
- [ ] Suggestions for fixing errors
- [ ] Graceful handling of partially valid files

**Error examples:**
```
Error: Invalid YAML syntax at line 15, column 3
  |
15| invalid: yaml: syntax
  |         ^ unexpected character ':'
  |
Suggestion: Check indentation and quote strings with special characters
```

### Stage 7: Additional Features (2-3 days)
**Tasks:**
- [ ] `format` command - reformatting without conversion
- [ ] `--pretty` flag for beautiful output
- [ ] stdin/stdout support
- [ ] Batch conversion of multiple files
- [ ] Dry-run mode

**Extended CLI:**
```bash
# Formatting
./config_converter format --input messy.json --output clean.json

# stdin to stdout
cat config.json | ./config_converter convert -f yaml

# Batch operations
./config_converter convert --input "configs/*.json" --output-dir yaml_configs/ -f yaml

# Dry run
./config_converter convert -i config.json -o config.yaml -f yaml --dry-run
```

### Stage 8: Testing and Documentation (2 days)
**Tasks:**
- [ ] Comprehensive test suite
- [ ] Property-based tests (if available in Gleam)
- [ ] Performance benchmarks
- [ ] README with usage examples
- [ ] API documentation

**Tests:**
- Unit tests for all modules
- Integration tests for CLI commands
- Edge cases (empty files, large files, malformed data)
- Cross-platform testing

### Stage 9: Packaging and Distribution (1 day)
**Tasks:**
- [ ] Create release binaries
- [ ] GitHub Actions for CI/CD
- [ ] Installation instructions
- [ ] Publish to Hex (optional)

## Additional Ideas for Extension

### Advanced features (if time permits):
- [ ] **Interactive mode** - step-by-step conversion with option selection
- [ ] **Custom schema DSL** - custom language for schema description
- [ ] **Plugin system** - support for custom formats
- [ ] **Config merging** - merge multiple configs
- [ ] **Diff mode** - compare configuration files
- [ ] **Template support** - configs with variables and substitutions

### Integrations:
- [ ] **VS Code extension** - real-time error highlighting
- [ ] **Pre-commit hook** - validate configs before commit
- [ ] **Docker image** - containerized version
- [ ] **Web interface** - simple web UI for conversion

## Useful Resources

### Gleam:
- [Gleam Language Tour](https://tour.gleam.run/)
- [Gleam Documentation](https://gleam.run/documentation/)
- [Gleam Packages](https://packages.gleam.run/)

### Libraries:
- [gleam_json](https://packages.gleam.run/packages/gleam_json)
- [argv](https://packages.gleam.run/packages/argv) - for CLI arguments
- [simplifile](https://packages.gleam.run/packages/simplifile) - file operations

### BEAM ecosystem:
- [Erlang YAML](https://github.com/yakaz/yamerl) - for YAML support
- [Erlang TOML](https://github.com/filmor/toml-erlang) - for TOML support

## Definition of Done

**Minimum MVP:**
- [x] JSON ↔ YAML ↔ TOML conversion
- [x] Basic type validation
- [x] CLI with readable errors

**Production ready:**
- [x] Comprehensive error handling
- [x] Full test coverage
- [x] Documentation and examples
- [x] Cross-platform binaries

**Showcase quality:**
- [x] Advanced validation with custom rules
- [x] Interactive mode or web interface
- [x] Benchmarks and optimizations

Good luck with the project! 🚀