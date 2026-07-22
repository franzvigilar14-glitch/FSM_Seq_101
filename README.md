# Sequence Detector 101 (Moore FSM)

A Moore finite state machine that detects the non-overlapping bit sequence **101** in a serial input stream, arriving LSB-first.

## Description

This module monitors a single-bit serial input (`in`) on each clock edge and asserts its output (`out`) for one clock cycle whenever it detects the pattern `1 → 0 → 1` has just completed, non-overlapping (once a match is found, detection restarts from scratch rather than reusing part of the matched sequence).

## Ports

| Port | Direction | Width | Description |
|---|---|---|---|
| `clk` | input | 1 | Clock signal |
| `rst_n` | input | 1 | Active-low asynchronous reset |
| `in` | input | 1 | Serial input bit stream |
| `out` | output reg | 1 | High for one cycle when sequence "101" is detected |
| `state` | output reg | 2 | Current FSM state (for display/debug) |

## FSM Design

**States:**
- `S0` (`2'b00`) — idle / no match yet
- `S1` (`2'b01`) — matched `1`
- `S2` (`2'b10`) — matched `10`
- `S3` (`2'b11`) — matched `101` → output asserted

**State transition logic:**

| Current State | in = 0 | in = 1 |
|---|---|---|
| S0 | S0 | S1 |
| S1 | S2 | S1 |
| S2 | S0 | S3 |
| S3 | S0 | S1 |

Output `out` is asserted (`1`) only in state `S3`, the cycle immediately after the full sequence `101` is detected.

## Structure

The design follows the standard **three-block FSM style**:
1. **Input/next-state block** (combinational) — determines `nxt` based on current state and input
2. **Sequential block** — updates the state register on `posedge clk` / `negedge rst_n`
3. **Output block** (Moore-style, combinational) — output depends only on current state, not input

## Author

Vigilar, Franz Louis G. — 31 March 2026
