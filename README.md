# PipAsp-freefem

Axisymmetric harmonic viscoelastic simulation for a pipette-aspiration-style setup, implemented in FreeFem++

## Scope

The script `PipAsp_FreeFem.edp` solves frequency-domain displacement of a 2D axisymmetric specimen under sinusoidal pressure on the pipette inner boundary.

## Model Definition

- Unknown field: complex displacement vector `[u1, u2]`.
- Formulation: linear viscoelastic harmonic balance with inertia.
- Axisymmetric weighting: all weak-form integrals include `2*pi*x`.
- Frequency sweep: predefined list from 0 Hz to 700 Hz.

Weak form components:

1. Elastic term with Lame parameters `mu`, `lambda`.
2. Inertial term `-omega^2 * rho`.
3. Viscous term `i * omega * eta`.
4. Boundary traction on `pipetteInnerBoundary`.

## Geometry and Mesh

Geometry parameters in SI units:

- `radiusPipetteInner = 1.5e-3 m`
- `radiusPipetteOuter = 2.5e-3 m`
- `radiusDomain = 5.0e-2 m`
- `heightDomain = 1.0e-2 m`

Discretization controls:

- `nFine = 2.25e4 1/m`
- `nCoarse = 5.0e3 1/m`
- Optional adaptive refinement enabled by `adaptMesh = true`.

Boundary labels:

- `1`: pipetteInnerBoundary
- `2`: pipetteContactBoundary
- `3`: freeBoundaries
- `4`: bottomBoundary
- `5`: centralLineBoundary

## Material and Loading Parameters

- `E = 12e3 Pa`
- `nu = 4.99e-1`
- `rho = 1.00e3 kg/m^3`
- `eta = 5.00 Ns/m`
- `pressureBoundary = 25 Pa` (harmonic amplitude)

Derived quantities:

- `mu = E / (2*(1+nu))`
- `lambda = nu*E / ((1+nu)*(1-2*nu))`
- `omega = 2*pi*f`

## Boundary Conditions

- On `pipetteContactBoundary`: `u1 = 0`, `u2 = 0`
- On `centralLineBoundary`: `u1 = 0` (axisymmetry condition)
- On `bottomBoundary`: `u1 = 0`, `u2 = 0`

Applied load:

- On `pipetteInnerBoundary`: normal pressure `-pressureBoundary` in the `u2` direction.

## Outputs

Primary output file:

- `displacement_harmonic.csv`

CSV format:

- Header: `f;ux;uy;p;phase`
- Delimiter: semicolon `;`
- Precision: 12 digits

Column definitions:

- `f`: frequency in Hz
- `ux`: displacement amplitude at `(x0, y0)` in x-direction
- `uy`: displacement amplitude at `(x0, y0)` in y-direction
- `p`: pressure field value at `(x0, y0)`
- `phase`: phase angle of `u2` in radians (`atan2(imag(u2), real(u2))`)

Measurement point (default):

- `x0 = 0.0 m`
- `y0 = 0.0 m`

During execution, the script also:

- Prints per-frequency diagnostics to stdout.
- Plots mesh and displacement-magnitude visualization.

## Requirements

- FreeFem++ available in PATH as `FreeFem++.exe`.
- Windows PowerShell or terminal capable of launching FreeFem++.

## Run

From repository root:

```powershell
FreeFem++.exe "PipAsp_FreeFem.edp" -cd
```

`-cd` runs with working directory set to the script directory, so output files are generated in the repository root.

## License

MIT License. See `LICENSE`.
