# ASM

These conventions apply to project-owned source-level and inline assembly. Third-party code should not be reformatted merely to match this document.

## 6502 Assembly (.s)

- Start by declaring the target CPU with `.setcpu "6502"` before other directives.
- Use semicolon comments, with banner-style separators to organize sections like code, data, and vectors.
- Constants are defined in uppercase with `=` and hexadecimal literals are prefixed with `$` (e.g., `DEST = $0200`).
- Segment directives (`.segment "CODE"`, `"RODATA"`, `"VECTORS"`) are used to separate executable code, data, and interrupt vectors.
- Labels are left-aligned with a trailing colon; instructions are indented for readability.
- Inline comments describe register intent and control flow, especially around branches and stack setup.
- Loops use clearly named labels like `copy_loop` and `forever` with a tight branch/jump structure.
- Null-terminated strings are emitted with `.byte` and a trailing `0` sentinel in the data segment.
- Interrupt handlers are minimal stubs using `rti`, and vectors are defined with `.word` entries in the `VECTORS` segment.

```asm
        .setcpu "6502"

DEST    = $0200          ; RAM address for output

        .segment "CODE"
reset:
        sei               ; disable IRQs
        ldx #$FF
        txs

copy_loop:
        lda message, y
        beq done
        sta DEST, y
        iny
        bne copy_loop

done:
        jmp done

        .segment "RODATA"
message:
        .byte "Hello", 0

        .segment "VECTORS"
        .word reset
```
