# Bash

Follow the project's existing shell conventions when they differ. Use POSIX `sh` instead when Bash-specific features are unnecessary and portability is required.

## Core Guidelines

### Code Style

- Start executable scripts with `#!/usr/bin/env bash` and commit them with the executable bit set.
- Enable `set -o errexit`, `set -o nounset`, and `set -o pipefail` when immediate failure is appropriate. Handle expected failures explicitly.
- Quote expansions by default: `"$value"`, `"${items[@]}"`, and `"$(command)"`.
- Use arrays for lists of arguments or paths. Do not store commands in strings or use `eval`.
- Use `[[ ... ]]` for Bash conditionals, `(( ... ))` for arithmetic, and `$(...)` for command substitution.
- Prefer `printf` over `echo` when printing variable content.
- Use `read -r` so backslashes in input are not interpreted.
- Validate arguments, environment variables, files, and commands before doing expensive or destructive work.
- Write intended output to standard output and diagnostics to standard error. Never print secrets or trace credential-handling code with `set -x`.
- Keep scripts focused. Use another language when data processing or application logic becomes complex.
- Run `shellcheck` and `shfmt` before submitting changes.

### Naming

- Use `snake_case` for functions and local variables.
- Use `UPPER_SNAKE_CASE` for exported variables and constants.
- Declare function variables with `local`.

### Paths

- Do not assume the current working directory. Resolve project-owned paths relative to the script.
- Pass `--` before user-controlled paths when supported. Avoid destructive commands with unresolved variables, broad globs, or unchecked targets.
- Create temporary files and directories with `mktemp` and remove them with an `EXIT` trap.
- Use null delimiters when passing arbitrary filenames through a pipeline. Do not parse `ls` output.

Use `BASH_SOURCE[0]` rather than `$0` to locate the current script, including when the file is sourced:

```bash
SCRIPT_DIR="$(cd -- "$(dirname -- "${BASH_SOURCE[0]}")" && pwd -P)"
readonly SCRIPT_DIR
```

### Example

```bash
#!/usr/bin/env bash

set -o errexit
set -o nounset
set -o pipefail

usage() {
  printf 'Usage: %s INPUT_FILE OUTPUT_DIR\n' "${0##*/}" >&2
}

main() {
  if (( $# != 2 )); then
    usage
    return 2
  fi

  local input_file=$1
  local output_dir=$2

  if [[ ! -f $input_file ]]; then
    printf 'Input file does not exist: %s\n' "$input_file" >&2
    return 1
  fi

  mkdir -p -- "$output_dir"
  cp -- "$input_file" "$output_dir/"
}

main "$@"
```
