# Quantum Field

A playable quantum mechanics sandbox in one HTML file. Glowing probability clouds are solutions of the
Schrödinger equation computed live. Click one and it collapses to a single particle.

**Run it:** open `index.html` in any modern browser. There's nothing to build or install. (The microphone feature
needs `https://` or `localhost`, e.g. `npx http-server`.)

## Play

| Do this | What happens |
| --- | --- |
| **Drag** | Throws a wave packet. Longer drag = faster particle with tighter ripples. |
| Drag again with the **same color** | Adds to the *same* particle's superposition. Where the waves overlap you see interference stripes. |
| **Click** | Looks for the particle. The % by the cursor is the chance of finding it there. If it's found, the whole cloud collapses to a dot. If not, a hole appears where you looked. |
| **Hold** | Keeps watching. A watched particle can't escape (quantum Zeno effect). Move slowly to herd it around. |
| **Space** | Observes everything at once. |
| **Scenes** | Double slit (with "Peek at slits" to destroy the pattern), tunneling, harmonic trap (a Schrödinger's-cat state), stadium billiard, entangled pair. |
| **Spin** | Throws vortex waves that carry angular momentum. Phase view shows them as rainbow spirals. |
| **Forces** | Makes different-colored particles push each other away or pull each other in. |
| **Energy** | Shows each particle's energy and spin. In the trap it shows the allowed energy levels, and **Measure energy** freezes the particle into one of them. |
| **Entangled pair** | Two particles on two wires sharing one wavefunction. Finding one changes the other. |
| **Share** | Gives a code (or link) that rebuilds your scene, walls and thrown waves. |
| **Mic** | Noise shakes the clouds, and a clap collapses them. |

Press **?** in the app for the full explanation.

## The physics

- **Evolution:** `iħ ∂ψ/∂t = −(ħ²/2m)∇²ψ + Vψ` on a 256×128 grid (ħ = m = 1), solved with the split-operator Fourier
  method. Each step is unitary and second-order accurate. A radix-2 FFT is written from scratch in the file.
  Free packets match theory: group velocity `v = ħk/m` and width `σ(t) = σ₀√(1 + (t/2σ₀²)²)` to floating-point precision.
- **Born rule:** brightness is `|ψ|²`. The moving ripples are the phase, `arg ψ`.
- **Measurement:** a click is a POVM with a soft detector window `W`. It clicks with probability `Σ W|ψ|²`.
  - On a click, a Gaussian position measurement collapses ψ onto the sampled point.
  - On no click, `ψ ← √(1−W) ψ`, which is an interaction-free measurement.
- **Zeno dynamics:** a hold is one projective "is it inside?" look. After that, the Zeno limit of continuous
  observation restricts the Hamiltonian to `PHP + QHQ`, which is a hard wall at the edge of the watched region.
- **Distinguishability:** each color is a separate wavefunction. Same-color waves interfere and different colors don't.
- **Double slit:** the screen records the probability flux absorbed at the right edge, and each run samples one hit from it.
  "Peek" is a which-path measurement (upper or lower half) just after the slits.
- **Boundaries** are absorbing layers, so probability that leaves the box is gone.
- **Spin:** a vortex packet `((x−x₀) ± i(y−y₀))·Gaussian` carries angular momentum ±ħ (the meter reads 0.996ħ).
- **Forces:** mean-field (Hartree) interaction through an FFT convolution with a Gaussian kernel. It pushes and pulls but by
  construction cannot entangle.
- **Energy:** ⟨T⟩ is computed exactly in k-space and ⟨V⟩ in real space. In the trap, ψ is projected onto Hermite-Gauss
  eigenstates with energy ħω(nx + ny + 1). The starting cat state occupies only even levels, and measuring the energy projects
  onto one level, which is a stationary state.
- **Entangled pair:** the full two-particle wavefunction ψ(x₁, x₂) on a line, evolved with H = p₁²/2 + p₂²/2 + V(x₁ − x₂) on
  a 128×128 grid. The wires show the marginals and the inset shows |ψ(x₁, x₂)|².

Open the dev console and use `QF` to poke at the internals, for example `QF.particles[0].re`.
