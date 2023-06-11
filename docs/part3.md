# Part III: Numerical precision in _ab initio_ calculations.

In this part you will learn about several new quantities and tools for working with numerical convergence in NOMAD.
These tools will help you

1. query for the level of precision or conformity that you need.
2. deploy extracted data into a notebook to start performing data science.

Your starting point is the Entries page > FILTERS (side menu) > Precision.
To navigate to the Entries page, check out [Part I - Exploring NOMAD](part1.md#entries_section).

<!-- Screenshot of the Precision section -->

Since these quantities are new and might not be commonly known, the emphasis is placed here on their definitions, as well as, the bare minimum of theoretical knowledge required to handle them.
There are also a couple example instructions guiding you to find a specific entry or put the data to use in a notebook.
Also watch out for the boxes with a pencil sign.
They delve deeper into some topics that are too tangential for the tutorial.

## Reciprocal space

In periodic systems, the most universal numerical parameter is the integration of the reciprocal, or k-space, and its sampling.
Since most times, the sampling points are spaced at fixed intervals, one can define a homogeneous _k-density_
$= \frac{\text{no. k-points}}{||\text{k-lattice vector}||}$.
As the k-density ramps up, the Bloch wave function converges. <!-- phrase better -->
Note that each dimension has its own k-density, which do not have to coincide.
To ensure that NOMAD users obtain data that meets their convergence needs, **k-line density** only shows the lowest value.

<p align="center">
    <img src="../assets/part3_convergence/klinedensity.png" alt="Schematic representation of k-line density calculation." width="65%" title="k-line density for 3D and 2D lattices">
</p>

!!! note "Band structure calculations"
    For their spatial resolution, band structures instead sample along line paths connecting several high-symmetry points.
    Moreover, line paths can differ in spacing.
    To accommodate them, NOMAD first projects the k-space grids onto the axes, and then proceeds as usual. <!-- double-check -->

## Electronic Structure {#elec_section}

At the level of the unit cell, there are several paradigms on how to represent the electronic wave function.
In this tutorial, we explore basis sets that start from plane waves, which mathematically mix well with the Bloch convolution.
In particular, we address _projector-augmented waves_ (PAW) and _augmented plane waves_ (APW).
As their names suggest, both extend the regular plane waves in regions where convergence is slow, namely around atomic nuclei.

A big distinction between PAW and APW is where they each draw this divide:

- PAW: the deciding factor is the orbital energies, with those below a certain threshold accounted for in special-purpose pseudopotentials.
  More on this in [Pseudopotentials](#pseudo_section).
- APW: here the divide is spatial in nature.
  A (mostly) spherical region, i.e. the muffin-tin sphere, is drawn surrounding the nuclei.
  The valence electrons reside in interstitial region between these spheres. 

### Valence Electrons

The main parameter controlling the plane waves is the longest k-vector $\mathbf{G}^{max}$.
A basis set of plane waves is then generated at fixed intervals, i.e. the secondary parameter, up to $\mathbf{G}^{max}$.
Since the sampling is symmetric in each direction, the length, $||\mathbf{G}^{max}||$, suffices.
By convention, most plane wave (especially PAW) codes express the vector length in energy units, which NOMAD reports as **plane-wave cutoff**,
$E^{max}_{cut} = \frac{\left(\hbar ||\mathbf{G}_{cut}^{max}||\right)^2}{2m_e}$.

<p align="center">
    <img src="../assets/part3_convergence/planewave.png" alt="Represenation of G^max sphere in reciprocal space, overlayed upon a pseudopotential landscape in real space." width="45%" title="plane wave sampling over pseudopotentials">
</p>

In principle, one can follow the same reasoning for the APW paradigm.
However, the cutoff energy alone gives an incomplete view.
While in PAW the plane waves sample the whole supercell, in APW they are barred from the muffin-tin spheres.
The convention here is to compare the length of $G^{max}$ to the largest muffin-tin radius in reciprocal space, yielding the unit-less fraction, **APW cutoff**,
$2\pi \frac{||\mathbf{G}_{cut}^{max}||}{||\mathbf{G}_{MT}^{max}||} = ||\mathbf{R}_{MT}^{min}|| \cdot ||\mathbf{G}_{cut}^{max}||$.

<p align="center">
    <img src="../assets/part3_convergence/muffintin.png" alt="Represenation of G^max sphere in reciprocal space, overlayed upon a muffin-tin potential landscape in real space." width="50%" title="plane wave sampling over a muffin-tin potential">
</p>

Both cutoff types can safely be increased to retrieve entries with progressively better converged valence electron wave functions.

!!! note "What about _grid spacing_?"
    At the moment, the mesh of the reciprocal or fast Fourier-transformed (FFT) space is not yet extracted.
    It is on the _planned feature_ list, though with low priority, considering that most of the convergence is already captured by the cutoff.
    If an upload or analysis ever requires a new feature, feel free to reach out to us via [fairmat@physik.hu-berlin.de](fairmat@physik.hu-berlin.de).

### Core Electrons

The mathematical parameters describing the electronic core region are extracted, but do not appear in the side menu. <!-- core-electron treatment still exists in DFT -->
To access them, select an entry, e.g. type `entry_id = XXXX` in the search bar > OVERVIEW > DATA.
In the DATA browser follow **run** (all computational data) > **method** ( metadata describing the calculation setup) > **electrons_representation**.
This sections contains metadata on the mathematical description of the electronic structure.

<!-- GIF showing browsing -->

Each representation comes with a **scope** and **type**, which specify the entity (e.g. wave function, density, exchange-correlation density, integration grid) and the overall basis set, respectively.
Hardly any code sticks to a single set of parameters values, instead adapting them according to task at hand.
For this reason there can be multiple electrons representations, each with their unique scope.
Those reported in the search are always the settings for `scope = wavefunction`.

As you have learned in [Electronic Structure](#elec_section), some basis sets divide the orbital set into widely different approaches.
Each **basis_set** subsection describes and individual region.
Here too, you will find **scope** and **type** with pretty much the same definitions.
Take note though, since the basis set **scope** refers to the region it encodes.
Examples include cases mentioned above, based on

- orbital energy: _core_ vs _valence_.
- spatial boundaries: _muffin-tin_ vs _interstitial_
- Hamiltonian: _kinetic_ and _electron-nucleus_ interaction vs _electron-electron_ interaction in the case of CP2k's Quickstep algorithm.

#### Muffin-tin spheres

APW is an all-electron approach, meaning that all orbitals are relaxed during an electronic self-consistent routine.
By itself, APW is no longer state-of-the-art and has been followed up by extensions such as LAPW, SLAPW, and APW+lo.
In this tutorial, APW has been used as a shorthand for this entire family of approaches and not just the progenitor.

While a full overview of the theory is beyond the scope of this tutorial (the interested reader is referred to XXXX) a quick rundown will explain the NOMAD design choices.
Essentially, APW exploits the spherical symmetry of the muffin-tin to simplify the Schrödigner or Dirac equation into a _radial_ and _spherical_ part.
The spherical equation is solved using a harmonics basis, where the main parameter is the harmonics cutoff.
The radial orbitals for each principal quantum number $n$, meanwhile, have to match up with their plane-wave counterparts at the border.
For technical reasons, this becomes a cumbersome requirement.
The workaround in these extensions is to parametrize the energy parameter and add higher-order derivative corrections.

<!-- Figure showing the MT orbitals -->

Different flavors come with different orders, e.g. LAPW up to first derivative, and different boundary conditions, such as _local orbitals_ (lo).
The latter offer a flexible way for complementing the basis set and are especially popular for describing semi-core states, i.e. muffin-tin states that are too diffuse to be well-contained, as well as unoccupied states.

While some APW require a manual setup for each orbital, e.g. Wien2k, others use a couple of steering parameters to generate the orbitals.
NOMAD typically opts for as concise as possible parameters, though in this case this is not practical for two reasons:

1. There is no consensus on which steering parameters to use. Each code allows different levels of customization.
2. The initial energy parameters are not commonly not directly used in the SCF routines.
   Rather, the code will have some algorithm that optimizes the energy parameter based on the geometry.
   As such, the initial orbital degeneracy is lifted.

Therefore, NOMAD "unrolls" the steering parameters down to individual radial orbitals, identified by their _type_ (e.g. APW, LAPW, lo), associated _harmonic index_ $l$, _derivative order_.

<!-- Figure showing the unrolling -->

#### Pseudopotentials {#pseudo_section}

With the exception of APW, most plane wave codes hide the core structure via an effective potential for the valence electrons.
Where most of these older pseudopotentials where constrained in their range of applications, those built for projector-augmented waves exhibit the highest degree of flexibility.

Where before NOMAD would simply indicate the usage of pseudopotentials, it now gives a more complete description, such as:
the _title_, the _density functional_ (or sometimes GW) used to generate the pseudopotential, _projector_ information, the recommended _minimum plane wave cutoff_ to be used for the valence electrons, and whether or not it is _norm-conserving_.
The latter is a useful (but more expensive) integration property and is a prerequisite for some methods. 
All of this metadata can be found under **run** > **method** > **electrons_representaion** or **atom_parameters** (describes the calculation setup by elemental type).

!!! note "Why am I seeing double quantities?"
    The NOMAD metainfo is semantically constructed. Consequentially, a quantity may belong under several categories.
    In this case, they always share the same name and definition though.
    Meanwhile, some quantities (e.g. **pseudopotential** vs **pseudopotential_name**) may appear to be similar, but are not exactly identical.
    Likely, one of them is legacy (**pseudopotential_name**). The legacy quantity will then be deprecated, though their actual removal may be scheduled later for compatibility reasons.

!!! note "Disclaimer"
    At the moment, parsing is restricted to VASP pseudopotentials, including non-standard or self-made versions.
    Wider code-support is being built out.
    NOMAD takes great care in complying with the copyright of the standard POTCAR files and their distribution, so all instances are stripped down to their metadata alone.

## Code-specific tiers

Filtering for the aforementioned quantities requires quite some expertise, and often it is hard to weigh the significance of two parameters to the electronic convergence.
Some codes, however, have benchmarked their own suggested settings into a list of increasing precision, i.e. _tiers_.
Lower tiers typically exist to facilitate better run speeds in long simulations or provide an initial starting point for higher tiers to start from and provide high-quality data.
The main purpose of these tiers, though, is to facilitate standardization and interoperability among code users.

To filter by tier, type `<code name> - <tier name>` (e.g. `VASP - accurate`) into the NOMAD side menu > Precision > Code-specific Tier.
`<code name>` is integral to this combination, because tiers are code-specific and cannot be compared cross-code.
Lastly, note that `<tier name>` is case-sensitive, but suggestions will pop up once you start typing.

!!! note "Tier matching"
    Contrary to other Precision quantities like k-line density, plane-wave cutoff, or APW cutoff, tiers cannot be graded on a continuous scale.
    Since tiers represent points in a high-dimensional, there is no straightforward way to deviations from these standards.
    Therefore, NOMAD only assigns perfect matches.
    Even one altered parameter by the user, disqualifies it from the tier categorization (though the data will likely still be very valid and valuable).
