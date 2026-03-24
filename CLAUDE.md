# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Architecture

Single-file app — all HTML, CSS, and JavaScript live in `index.html`. No build step, no package manager, no framework. Only external dependency is a Google Fonts CDN link (Inter).

Open `index.html` directly in a browser to develop.

## Structure

Two tabs:

1. **Distance Converter** — bidirectional m/yd conversion with unit-toggle buttons (clicking either unit button swaps from/to). Two row pairs: Carry and Total. A club selector pre-fills carry from PGA Tour averages and shows delta comparison tiles (user carry vs PGA/LPGA averages). Second card handles mph ↔ m/s speed conversion with the same toggle pattern.

2. **Launch Optimizer** — physics simulation taking ball speed (mph), launch angle (deg), backspin (rpm) → carry in yards/meters + peak height + SVG trajectory chart.

## Key data

`TOUR` object in `<script>` stores 2023 PGA/LPGA averages keyed by club name: carry (yd), ball speed (mph), launch angle (deg), spin (rpm) for both tours.

## Physics model (`simulate()`)

2D Euler integration at 2ms time steps:
- **Drag**: `Fd = 0.5 * ρ * A * Cd * v²`, `Cd = 0.23` (Erlichson 1983, dimpled ball)
- **Magnus lift**: `Fl = 0.5 * ρ * A * Cl * v²`, `Cl = 0.53 * S^0.55` where `S = r·ω/v` (calibrated to Bearman & Harvey 1976)
- Spin magnitude held constant (no spin decay)
- Sea-level air density only (`ρ = 1.225 kg/m³`)

Calibrated against Trackman 2023 data at 0° attack angle:
- 160 mph / 11.7° / 2600 rpm → ~269 yd carry
- 130 mph / 12.8° / 2750 rpm → ~205 yd carry
- 100 mph / 15.9° / 2600 rpm → ~140 yd carry

Changing `Cd` or the `Cl` exponent affects all club results proportionally. The exponent 0.55 gives `Cl ≈ 0.14` at driver conditions (S ≈ 0.08), matching published aerodynamic data for dimpled golf balls.
