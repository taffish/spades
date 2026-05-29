# taf-spades

TAFFISH app for SPAdes, the genome assembler toolkit for isolated genomes,
single-cell data, metagenomes, transcriptomes, plasmids, viral genomes,
biosynthetic gene clusters, and related short-read assembly modes.

## Package Identity

- name: `spades`
- command: `taf-spades`
- TAFFISH version: `4.2.0-r1`
- kind: `tool`
- container image: `ghcr.io/taffish/spades:4.2.0-r1`
- upstream: SPAdes `v4.2.0`
- runtime version: `SPAdes genome assembler v4.2.0`
- default command: `spades.py`
- TAFFISH app license: Apache-2.0
- upstream license: GPL-2.0-only

## Installation

```bash
taf install spades
taf-spades --help
```

The app uses a thin TAFFISH container wrapper:

```taf
<taf-app:container:ghcr.io/taffish/spades:4.2.0-r1>
spades.py ::*ARGV*::
```

Wrapper-level help and upstream help are separate:

```bash
taf-spades --help
taf-spades --version
taf-spades -- --help
taf-spades spades.py --help
```

Because `command_mode = true`, helper commands in the same SPAdes runtime are
available directly:

```bash
taf-spades metaspades.py --help
taf-spades plasmidspades.py --help
taf-spades rnaspades.py --help
taf-spades spades-kmercount --help
taf-spades binspreader --help
taf-spades pathracer --help
taf-spades spaligner --help
```

## Common Workflows

Run the official SPAdes installation test:

```bash
taf-spades -- --test -t 1
```

SPAdes writes this official test to `spades_test/`; upstream 4.2.0 rejects
using `--test` together with `-o`.

Assemble paired-end Illumina reads:

```bash
taf-spades -1 reads_1.fq.gz -2 reads_2.fq.gz -t 8 -m 32 -o spades_out
```

Use the explicit command-mode form, useful in scripts and flows:

```bash
taf-spades spades.py -1 reads_1.fq.gz -2 reads_2.fq.gz -t 8 -m 32 -o spades_out
```

Run selected SPAdes modes:

```bash
taf-spades spades.py --isolate -1 reads_1.fq.gz -2 reads_2.fq.gz -o isolate_out
taf-spades metaspades.py -1 reads_1.fq.gz -2 reads_2.fq.gz -o meta_out
taf-spades plasmidspades.py -1 reads_1.fq.gz -2 reads_2.fq.gz -o plasmid_out
taf-spades rnaspades.py -1 reads_1.fq.gz -2 reads_2.fq.gz -o rna_out
```

## Packaged Commands

The image is built from the official SPAdes `v4.2.0` source release and places
`/opt/spades/bin` on `PATH`. It includes:

- pipeline front ends: `spades.py`, `metaspades.py`, `plasmidspades.py`,
  `rnaspades.py`, `metaviralspades.py`, `metaplasmidspades.py`,
  `rnaviralspades.py`, `coronaspades.py`
- core binaries and helpers: `spades-core`, `spades-hammer`,
  `spades-corrector-core`, `spades-ionhammer`, `spades-kmercount`,
  `spades-kmer-estimating`, `spades-read-filter`, `spades-gbuilder`,
  `spades-gmapper`, `spades-gsimplifier`, `spades-gfa-split`
- additional non-MPI SPAdes toolkit projects: `binspreader`, `pathracer`,
  `pathracer-seq-fs`, `spaligner`
- runtime tools: `python3`, `bash`, `gzip`, `bzip2`, `xz`

The build keeps SPAdes installed files, bundled configs, HMM resources,
pipeline Python modules, and official test datasets under `/opt/spades`.

## Inputs and Outputs

SPAdes accepts FASTQ/FASTA input in the modes documented upstream. Common
short-read inputs are `-1`/`-2` paired reads or `-s` single reads; hybrid
assemblies can add PacBio or Nanopore reads as supplementary input where
supported by upstream SPAdes.

SPAdes writes a project output directory. Typical files include
`contigs.fasta`, `scaffolds.fasta`, `assembly_graph.fastg`,
`assembly_graph_with_scaffolds.gfa`, `params.txt`, and `spades.log`.

SPAdes refuses to reuse some existing output directories unless upstream
restart/continue options are used. Keep one output directory per run unless
you intentionally use SPAdes restart/resume behavior.

## Platform

This app is compiled from source on Debian 12 and declares native
`linux/amd64` and `linux/arm64` image builds. The official upstream GitHub
release also provides a large prebuilt Linux archive, but this TAFFISH app
uses the source release so the published image can be built reproducibly for
both declared Linux platforms.

## Boundaries

SPAdes is primarily intended for second-generation sequencing data such as
Illumina and IonTorrent. Long PacBio or Oxford Nanopore reads are supported by
upstream SPAdes as supplementary reads in hybrid workflows, not as a general
long-read-only assembler. Use dedicated long-read assemblers such as hifiasm,
Flye, or Verkko when that is the intended analysis.

This app does not download reads, references, databases, or annotation
resources at runtime. It does not run downstream assembly evaluation,
annotation, binning QC, or visualization; combine it with other TAFFISH apps
such as `quast`, `seqkit`, `bandage-ng`, or `busco` as needed.

The app builds the default SPAdes projects plus `binspreader`, `pathracer`,
and `spaligner`. It does not include `hpcSPAdes`, because that target requires
an MPI-enabled cluster-style runtime that should be packaged and documented
separately if needed. Direct NCBI SRA input support is also not enabled because
the upstream source build keeps `SPADES_USE_NCBISDK` disabled by default; use
FASTQ/FASTA inputs or convert SRA data explicitly before running this app.

Large real assemblies require substantial memory and disk. The smoke test only
validates installation, helper availability, dynamic libraries, and the
official small SPAdes test dataset; it does not benchmark assembly quality or
resource scaling.

## Smoke Coverage

Smoke checks verify:

- SPAdes version file and `spades.py --version` report `4.2.0`.
- main pipeline help and selected mode help pages are present.
- core binaries and helper tools exist and have no missing shared libraries.
- the official `spades.py --test -t 1` path writes non-empty `contigs.fasta`
  and `scaffolds.fasta`.
- each smoke entry is self-contained and can run in a fresh container.

## Upstream

- homepage and manual: <https://ablab.github.io/spades/>
- repository: <https://github.com/ablab/spades>
- release: <https://github.com/ablab/spades/releases/tag/v4.2.0>
- source download: <https://github.com/ablab/spades/releases/download/v4.2.0/SPAdes-4.2.0.tar.gz>
- source SHA256 used by this app: `043322129f8536411f1172b7d1c9adfcb6d49d152c10066ccc03e86b6f615a6b`

If you use SPAdes in research, cite Prjibelski et al. 2020 and the relevant
mode-specific SPAdes publications listed in the upstream README/manual.
