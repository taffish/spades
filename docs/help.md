taf-spades 4.3.0-r1

TAFFISH app for SPAdes v4.3.0, a genome assembler toolkit for
short-read isolate, single-cell, metagenomic, transcriptomic, plasmid,
viral, biosynthetic, and related assembly modes.

Usage:
  taf-spades [-h | --help]
  taf-spades [-v | --version]
  taf-spades -- [SPADES_ARGS...]
  taf-spades [SPADES_ARGS...]
  taf-spades COMMAND [ARGS...]
  taf-spades --compile [ARGS...]

Wrapper options:
  -h, --help       Show this TAFFISH app help
  -v, --version    Show TAFFISH package version
  --compile        Print generated shell code instead of running it
  --               Stop wrapper option parsing and pass following args to
                   the default upstream command

Default upstream command:
  spades.py

Upstream help and version:
  taf-spades -- --help
  taf-spades -- --version
  taf-spades spades.py --help
  taf-spades spades.py --version

Command mode:
  taf-spades spades.py --test -t 1
  taf-spades metaspades.py --help
  taf-spades plasmidspades.py --help
  taf-spades rnaspades.py --help
  taf-spades spades-kmercount --help
  taf-spades binspreader --help
  taf-spades pathracer --help
  taf-spades spaligner --help
  taf-spades splitter --help

Common workflows:
  taf-spades -- --test -t 1
  taf-spades -1 reads_1.fq.gz -2 reads_2.fq.gz -t 8 -m 32 -o spades_out
  taf-spades spades.py --isolate -1 reads_1.fq.gz -2 reads_2.fq.gz -o out
  taf-spades spades.py --frugal -1 reads_1.fq.gz -2 reads_2.fq.gz -o out
  taf-spades metaspades.py -1 reads_1.fq.gz -2 reads_2.fq.gz -o meta_out
  taf-spades plasmidspades.py -1 reads_1.fq.gz -2 reads_2.fq.gz -o plasmid_out
  taf-spades rnaspades.py -1 reads_1.fq.gz -2 reads_2.fq.gz -o rna_out
  taf-spades splitter graph.gfa slr_dataset.yaml splitter_out -Gblunt

Packaged commands:
  spades.py, metaspades.py, plasmidspades.py, rnaspades.py,
  metaviralspades.py, metaplasmidspades.py, rnaviralspades.py,
  coronaspades.py, spades-core, spades-hammer, spades-corrector-core,
  spades-ionhammer, spades-kmercount, spades-kmer-estimating,
  spades-read-filter, spades-gbuilder, spades-gmapper,
  spades-gsimplifier, spades-gfa-split, binspreader, pathracer,
  pathracer-seq-fs, spaligner, splitter,
  python3, bash, gzip, bzip2, xz.

Inputs and outputs:
  SPAdes accepts upstream-supported FASTQ/FASTA inputs. Common inputs are
  -1/-2 paired reads or -s single reads; some modes accept supplementary
  PacBio or Nanopore reads. Outputs are written under the selected -o
  directory, commonly including contigs.fasta, scaffolds.fasta, assembly
  graph files, params.txt, and spades.log.

Platform:
  Built from the official SPAdes v4.3.0 source release on Debian 12.
  Native image builds are declared for linux/amd64 and linux/arm64.

Boundaries:
  SPAdes is mainly for second-generation sequencing data. Long reads are
  supplementary in supported hybrid modes, not a replacement for dedicated
  long-read assemblers. This app does not download reads, databases, or
  references, and it does not run downstream assembly QC or annotation.
  Use separate tools such as quast, seqkit, bandage-ng, or busco as needed.
  SPAdes 4.3.0 adds --frugal and the SPlitteR splitter tool. hpcSPAdes is
  not included because it needs MPI; direct SRA input support is not enabled
  because the source build leaves the NCBI SDK option off. Upstream SPAdes
  writes --test output to spades_test/ and rejects combining --test with -o.

Smoke:
  Smoke validates version/help, --frugal, splitter help, major helper commands,
  missing shared-library checks, and the official spades.py --test path.
