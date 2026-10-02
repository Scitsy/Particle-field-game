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
| **Scenes** | Double slit (with "Peek at slits" to destroy the pattern), tunneling, harmonic trap (a Schrödinger's-cat state), stadium billiard. |
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

Open the dev console and use `QF` to poke at the internals, for example `QF.particles[0].re`.
