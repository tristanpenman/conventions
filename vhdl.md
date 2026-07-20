# VHDL

These conventions apply to project-owned VHDL files. Third-party code should not be reformatted merely to match this document.

## Core Guidelines

### Code Style

- Use two-space indentation instead of tabs. Keep keywords lowercase.
- Always include `library ieee;` and `use ieee.std_logic_1164.all;` at the top of each module, with additional numeric packages as needed.
- Port lists are aligned and use named associations in `port map` blocks for clarity.
- Synchronous logic is expressed using `process(clk)` and `if rising_edge(clk)` with nested `if` statements for state updates.
- Constants are declared in lowercase with descriptive names for timing parameters and use integer types for readability.
- Vector ranges use `downto` consistently for buses and counters.

### Naming

- Use `snake_case` for entity, architecture, signal, constant, and instance names, and keep formatting consistent across declarations, processes, and `port map` blocks.
- Component instances are labeled with a `u_` or descriptive prefix (`clock`, `u_cpu`, `u_ppu`) and follow a consistent block structure.
- Signal names use lowercase with descriptive suffixes such as `_s`, and initialization is done inline where helpful.
- Active-low signals are indicated with a `_n` suffix and commented inline to explain polarity.

### Example

```vhdl
library ieee;
use ieee.std_logic_1164.all;

entity cpu_core is
  port (
    clk   : in std_logic;
    reset : in std_logic
  );
end cpu_core;

architecture rtl of cpu_core is
  signal enable_s : std_logic := '1';
begin
  u_cpu: entity work.T65
    port map(
      clk    => clk,
      res_n  => reset, -- active low
      enable => enable_s
    );
end rtl;
```

## Best Practices

### Prefer IEEE `numeric_std`

- Prefer `ieee.numeric_std` for arithmetic and type conversions. Avoid `STD_LOGIC_UNSIGNED` and `STD_LOGIC_ARITH`; they are non-standard packages and can conflict with `numeric_std` semantics, especially when mixing signed and unsigned math or converting between vector and numeric types.
- Use explicit `unsigned` and `signed` types where arithmetic is intended. This keeps intent clear, improves portability across current synthesis tools, and makes examples such as `flashy_lights`, `segment_counter`, `simple_vga`, and `volume_control` easier to reason about.

### Clock enables instead of deriving clocks from data bits

- Avoid treating counter bits, divided data signals, or pulses such as `vsync` as clocks. That pattern creates unnecessary clock domains and can violate FPGA clock routing expectations.
- Keep sequential logic synchronous to the board clock. When slower updates are needed, generate a clock-enable signal and use it inside the existing `rising_edge(clk)` process. Register external inputs such as joysticks in that same clock domain before using them.

### De-duplicate common logic with reusable packages and generics

- Factor recurring constants and structures into shared packages instead of copying them across designs. For example, a `vga_timing_pkg` can hold timing records for modes such as 640x480 and 800x600, while generics can parameterize widths, limits, and timing choices.
- This keeps updates in one place and avoids cloning files across different parts of a project.

### Strengthen verification

- Add a testbench for each module where practical. Avoid testbenches that drive a single input vector and halt, because they provide very little coverage and rarely catch regressions.
- Prefer self-checking testbenches, especially for timing-sensitive modules such as VGA controllers. Add simple assertions, counters, or scoreboards to document expected behavior, and run them under a modern simulator such as GHDL or Questa.
