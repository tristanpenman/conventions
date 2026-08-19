# Verilog

These conventions apply to project-owned Verilog files. Third-party code should not be reformatted merely to match this document.

## Core Guidelines

### Code Style

- Use two-space indentation instead of tabs. Keep keywords lowercase.
- Use ANSI-style module declarations, with port directions and types declared in the port list.
- Put one port or signal declaration on each line when declarations are not short, and align related declarations where this improves readability.
- Use named port connections for module instances. Avoid positional connections except for very small, local primitives.
- Express synchronous logic with `always @(posedge clk)` and use nonblocking assignments (`<=`) for registers. Use blocking assignments (`=`) in combinational `always @*` blocks.
- Give constants descriptive lowercase names and declare them with `localparam` unless callers are intended to override them.
- Declare vector widths and numeric literals explicitly. Use `[width - 1:0]` consistently for buses and counters.

### Naming

- Use `snake_case` for module, port, net, register, parameter, and instance names, and keep formatting consistent across declarations and instantiations.
- Prefix module instances with `u_` or use a descriptive instance name such as `u_cpu`, `u_ppu`, or `clock`.
- Use lowercase, descriptive signal names. Add suffixes such as `_r` for registered values and `_next` for next-state values where the distinction is useful.
- Indicate active-low signals with an `_n` suffix. Do not rely on comments alone to communicate polarity.

### Example

```verilog
module cpu_core (
  input  wire clk,
  input  wire reset_n,
  output wire ready
);
  reg enable_r;

  always @(posedge clk) begin
    if (!reset_n)
      enable_r <= 1'b1;
  end

  cpu u_cpu (
    .clk    (clk),
    .reset_n(reset_n),
    .enable (enable_r),
    .ready  (ready)
  );
endmodule
```

## Best Practices

### Make widths and signedness explicit

- Size constants and intermediate values explicitly, especially in arithmetic, comparisons, shifts, and concatenations. Unsized literals and implicit extension can produce different results when an expression changes width or signedness.
- Declare signed values with `signed` and convert deliberately at signed/unsigned boundaries. Treat compiler warnings about truncation, extension, and width mismatches as defects to investigate.
- Use parameters for configurable widths and derive dependent ranges from them, such as `[data_width - 1:0]`, instead of repeating numeric widths throughout a module.

### Clock enables instead of deriving clocks from data bits

- Avoid treating counter bits, divided data signals, or pulses such as `vsync` as clocks. That pattern creates unnecessary clock domains and can violate FPGA clock-routing expectations.
- Keep sequential logic synchronous to the board clock. When slower updates are needed, generate a clock-enable signal and test it inside the existing `always @(posedge clk)` block. Synchronize external inputs before using them in that clock domain.

### De-duplicate common logic with modules, parameters, and headers

- Factor recurring logic into reusable modules instead of copying it across designs. Use parameters for widths, limits, and timing choices.
- Keep shared constant and macro definitions in narrowly scoped header files when a reusable module is not appropriate. Guard headers against repeated inclusion and avoid broad macros that obscure types, widths, or control flow.

### Strengthen verification

- Add a testbench for each module where practical. Avoid testbenches that drive a single input vector and halt, because they provide little coverage and rarely catch regressions.
- Prefer self-checking testbenches, especially for timing-sensitive modules such as VGA controllers. Add checks, counters, or scoreboards to document expected behavior, and make failures terminate the simulation with a nonzero result where the simulator supports it.
- Run lint as part of normal verification. Enable warnings for inferred latches, incomplete combinational assignments, width mismatches, implicit nets, and unused signals.
