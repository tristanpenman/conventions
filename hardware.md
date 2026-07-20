# Hardware

These conventions apply to project-owned HDL (Hardware Definition Language) files, which includes VHDL and Verilog.

Third-party code should not be reformatted merely to match this document.

## VHDL (.vhd)

- Always include `library ieee;` and `use ieee.std_logic_1164.all;` at the top of each module, with additional numeric packages as needed.
- Entities and architectures use lowercase names with underscores (`px_pro`, `cpu_core`, `ppu_vga`) and `rtl` for the architecture name when hand-written.
- Port lists are aligned and use named associations in `port map` blocks for clarity.
- Signal names use lowercase with descriptive suffixes such as `_s`, and initialization is done inline where helpful.
- Component instances are labeled with a `u_` or descriptive prefix (`clock`, `u_cpu`, `u_ppu`) and follow a consistent block structure.
- Synchronous logic is expressed using `process(clk)` and `if rising_edge(clk)` with nested `if` statements for state updates.
- Constants are declared in lowercase with descriptive names for timing parameters and use integer types for readability.
- Vector ranges use `downto` consistently for buses and counters.
- Active-low signals are indicated with a `_n` suffix and commented inline to explain polarity.

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
