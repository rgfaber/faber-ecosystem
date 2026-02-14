# Faber Ecosystem

[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-support-yellow.svg)](https://buymeacoffee.com/rlefever)

<p align="center">
  <strong>AI &amp; Neuroevolution for Erlang/OTP</strong>
</p>

---

## What is Faber?

Faber ("the maker" in Latin) is an **Erlang/OTP ecosystem for evolutionary neural networks**. It provides a complete stack for evolving adaptive AI controllers using TWEANN (Topology and Weight Evolving Artificial Neural Networks) and NEAT (NeuroEvolution of Augmenting Topologies).

The ecosystem enables:

- **Topology Evolution** - Networks grow and adapt their structure
- **Population-Based Training** - Evolve populations with speciation and selection
- **Liquid Time-Constant Neurons** - Temporal dynamics for sequence processing
- **ONNX Export** - Deploy evolved networks to production inference engines
- **Distributed Evaluation** - Optionally distribute fitness evaluation across a Macula mesh

## Architecture Overview

<p align="center">
  <img src="assets/faber-ecosystem-overview.svg" alt="Faber Ecosystem Architecture" width="100%">
</p>

## Packages

| Package | Description | Links |
|---------|-------------|-------|
| **faber_tweann** | TWEANN neural networks with LTC neurons, topology evolution, and ONNX export | [GitHub](https://github.com/rgfaber/faber-tweann) \| [HexDocs](https://hexdocs.pm/faber_tweann) |
| **faber_neuroevolution** | Population-based evolutionary training with speciation, selection, and meta-controllers | [GitHub](https://github.com/rgfaber/faber-neuroevolution) \| [HexDocs](https://hexdocs.pm/faber_neuroevolution) |

## Core Capabilities

### faber_tweann - Neural Network Foundation

- **TWEANN Architecture** - Topology and Weight Evolving Artificial Neural Networks
- **LTC Neurons** - Liquid Time-Constant neurons for temporal processing
- **Substrate Networks** - Spatial neural patterns with geometric regularity
- **Plasticity** - Hebbian learning and neuromodulation
- **Custom Activations** - Extensible activation function library
- **ONNX Export** - Export evolved networks for deployment

### faber_neuroevolution - Evolutionary Framework

- **NEAT Algorithm** - NeuroEvolution of Augmenting Topologies with historical markings
- **HyperNEAT** - Indirect encoding via CPPNs for large-scale networks
- **Speciation** - Protect innovation through species-based fitness sharing
- **Island Model** - Parallel populations with periodic migration
- **Novelty Search** - Behavior-driven exploration instead of pure fitness
- **Meta-Controller** - Adaptive parameter tuning during evolution
- **Liquid Conglomerate** - Multi-agent cooperative evolution

## Dependency Flow

<p align="center">
  <img src="assets/dependency-flow.svg" alt="Faber Dependency Flow" width="100%">
</p>

## Quick Start

### Erlang (rebar.config)

Add Faber packages to your `rebar.config`:

```erlang
{deps, [
    {faber_tweann, "~> 0.1.0"},
    {faber_neuroevolution, "~> 0.1.0"}
]}.
```

### Elixir (mix.exs)

```elixir
defp deps do
  [
    {:faber_tweann, "~> 0.1.0"},
    {:faber_neuroevolution, "~> 0.1.0"}
  ]
end
```

### Basic Usage

```erlang
%% Create a minimal genome
Genome = faber_tweann_genotype:construct(
    faber_tweann_morphology:xor_mimic(),
    <<"species_001">>
),

%% Compile to phenotype (runnable network)
Phenotype = faber_tweann_phenotype:from_genotype(Genome),

%% Activate with inputs
Outputs = faber_tweann_phenotype:activate(Phenotype, [0.5, 0.8]).
```

## Use Cases

- **Robotics** - Evolve controllers for locomotion, grasping, and navigation
- **Game AI** - Adaptive opponents that learn and improve over generations
- **Adaptive Controllers** - Self-tuning systems for dynamic environments
- **Swarm Intelligence** - Cooperative multi-agent behaviors via co-evolution
- **Signal Processing** - Temporal pattern recognition with LTC neurons
- **Anomaly Detection** - Novelty search for identifying unusual patterns

## Related Ecosystems

Faber is designed to work alongside these independent ecosystems:

### Macula - Decentralized Infrastructure ([macula-io](https://github.com/macula-io))

HTTP/3 mesh networking for distributing fitness evaluation across edge nodes. Faber populations can optionally use the Macula mesh for parallel evaluation.

> See [macula-ecosystem](https://github.com/macula-io/macula-ecosystem) for full documentation.

### Reckon - Event Sourcing & CQRS ([reckon-db-org](https://github.com/reckon-db-org))

BEAM-native event sourcing stack. Applications using Faber for AI can persist evolution history and training state using Reckon's event store.

> See [reckon-ecosystem](https://github.com/reckon-db-org/reckon-ecosystem) for full documentation.

### Hecate - AI-Powered Developer Studio ([hecate-social](https://github.com/hecate-social))

Developer studio that orchestrates venture lifecycles. Hecate can use Faber for AI-assisted code generation and adaptive development workflows.

> See [hecate-ecosystem](https://github.com/hecate-social/hecate-ecosystem) for full documentation.

## Academic References

The Faber ecosystem implements algorithms from foundational neuroevolution research:

- **NEAT** - Stanley, K.O. & Miikkulainen, R. (2002). *Evolving Neural Networks through Augmenting Topologies.* Evolutionary Computation, 10(2), 99-127.
- **HyperNEAT** - Stanley, K.O., D'Ambrosio, D.B. & Gauci, J. (2009). *A Hypercube-Based Encoding for Evolving Large-Scale Neural Networks.* Artificial Life, 15(2), 185-212.
- **TWEANN** - Stanley, K.O. & Miikkulainen, R. (2004). *Competitive Coevolution through Evolutionary Complexification.* JAIR, 21, 63-100.
- **Novelty Search** - Lehman, J. & Stanley, K.O. (2011). *Abandoning Objectives: Evolution Through the Search for Novelty Alone.* Evolutionary Computation, 19(2), 189-223.
- **LTC Networks** - Hasani, R. et al. (2021). *Liquid Time-constant Networks.* AAAI Conference on Artificial Intelligence.
- **Neuromodulation** - Soltoggio, A., Bullinaria, J.A., Mattiussi, C., Durr, P. & Floreano, D. (2008). *Evolutionary Advantages of Neuromodulated Plasticity in Dynamic, Reward-based Scenarios.* ALIFE XI.

## Community

- **Faber**: [rgfaber](https://github.com/rgfaber) on GitHub | Search `faber` on [hex.pm](https://hex.pm)
- **Macula**: [macula-io](https://github.com/macula-io) on GitHub
- **Reckon**: [reckon-db-org](https://github.com/reckon-db-org) on GitHub
- **Hecate**: [hecate-social](https://github.com/hecate-social) on GitHub
- **Issues**: Report bugs on the respective repositories

## License

MIT - See [LICENSE](LICENSE) for details.

---

<p align="center">
  <sub>Built with the BEAM</sub>
</p>
