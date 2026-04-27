# PR Title
Add TDR planning docs and KiCad prototype schematic

## Summary
This PR introduces the first documentation and hardware prototype artifacts for the Kabel-Tester TDR workflow.

## What Changed
- Expanded project README with:
  - BNC cable checker plan for cable break and short-circuit detection
  - TDR-based cable-break localization plan using signal propagation time
- Added dedicated hardware concept document for TDR:
  - `hw-concept-tdr.md`
- Added initial KiCad prototype project files:
  - `kicad/tdr-prototype/tdr-prototype.kicad_sch`
  - `kicad/tdr-prototype/tdr-prototype.kicad_pro`
  - `kicad/tdr-prototype/tdr-prototype-bom.csv`
  - `kicad/tdr-prototype/README.md`

## Scope
- Documentation and prototype design only
- No firmware or measurement algorithm implementation yet
- No validated production-ready PCB release

## Validation
- Files added and structured for direct opening in KiCad
- BOM included for V1 prototype bring-up
- README now contains measurable milestones and acceptance criteria for TDR work

## Risks / Notes
- Schematic is a V1 lab prototype and requires electrical review before fabrication
- Footprints and selected parts should be re-checked against procurement and target performance
- Signal integrity and ESD behavior are not yet lab-verified

## Next Steps
1. Verify schematic connectivity and component choices in KiCad ERC
2. Add PCB layout with controlled impedance guidance
3. Define MCU timing interface and initial measurement firmware
4. Validate with known-length reference cables and update calibration model
