

# **Introduction**

Welcome to the COMPADRE documentation homepage! As new components of this project get updated, information on how to utilize them can be found here. 


# **PRIMUS**

PRIMUS = **P**edigree **R**econstruction and **I**dentification of the **M**aximally **U**nrelated **S**et [of individuals].

PRIMUS 2.0 has been re-engineered to improve scalability, UX, and downstream analysis automation as a part of both the [COMPADRE](https://github.com/Gram-Labs/compadre) suite and the upcoming [BioSIM](https://github.com/Gram-Labs/biosim) ecosystem.

__Last updated__: 15 February 2021 (Version 2.0.0)

[COMPADRE home](https://compadre.dev) | [v1.9.0 home](https://primus.gs.washington.edu/primusweb/res/documentation.html) | [v1.9.0 paper](https://www.sciencedirect.com/science/article/pii/S0002929714004273)




## Code Organization

### Main scripts

Five 'main' scripts control the flow of analysis options/steps -  one that initializes the workspace and initiates the entire process, two that actually make calls to the helper script analysis functions, and two that compile and print output information. A brief overview of each of these scripts is tabulated below. Additionally, a setup script is available to simplify the setup process for program-wide variables and additional useful settings, and a separate script can be ran by toggling the --test argument to quickly check that all other scripts are compatible with one another prior to a run.

Script name             | Purpose
---                     | ---
`run.py`                | Initialize relevant file paths and start the main PRIMUS flow 
`main.py`               | Main PRIMUS analysis script
`find_pedigree.py`      | TBA
`summary.py`            | TBA
`summary_pairwise.py`   | TBA
`setup.py`              | Setup walkthrough via console prompts
`checktest.py`          | Test cross-compatibility of all scripts in the suite
`plink.py`              | Automate PLINK file formatting prior to running PRIMUS pipeline


### Helper scripts

Scripts containing helper functions for the majority of the PRIMUS analysis (called in the main scripts, never ran directly) are stored in the `/src/` directory. Each function in these scripts has a brief comment section explaining its functionality.

Script name             | Purpose
---                     | ---
`compare.py`            | TBA
`flags.py`              | TBA
`imus.py`               | TBA
`node.py`               | TBA
`preprimus.py`          | TBA
`primus_ersa.py`        | TBA
`reconstruct.py`        | TBA
`relationships.py`      | TBA
`pid_gen.py`            | Generate process IDs for logging/debugging help



## General Improvements

### Analytical Improvements (still need elaboration)

- pedigree-aware shared segment detection
- improved integration with ERSA and PADRE data streams (whole purpose of making COMPADRE a standalone code suite)
- data output integration with BioSIM API for ease of use with incoming biorepository data

### Scalability Improvements

- Lighter-weight code architecture to handle running larger jobs at scale
- Improved, all-inclusive error handling and data quality control measures at all steps of the PRIMUS analysis process (integration of PRIMUS-QC)
- Tackling n+1 problem
- More efficient data storage (both as input and output)

### Runtime Improvements

- Improving helper function big-O times

### UX Improvements

- minimize clutter by reducing number of unique files
- combine subroutines from the former `perl_modules` folder to reduce total code length
- add custom execution framework/guidelines based on detected OS

### Composite Relatedness Probability Calculation 

123

### Data Simulation Pipeline

123



## API Implementation

123



## Perl → Python Conversions

### Main script changes**

Perl module name changes and package replacements are summarized below. Full documentation on the changes can be found [here](https://docs.google.com/document/d/1HU8exn5EBTGSv24Eyx5GMZZOt59WFLDpPYGT_mEtrKU/edit?usp=sharing).
 
Perl script name                      | Python script name
---                                   | ---
`run_PRIMUS.pl`                       | `run.py`
`primus_kickoff7.pl`                  | `main.py`
`find_expected_pedigree.pl`           | `find_pedigree.py`
`make_dataset_summary.pl`             | `summary.py`
`make_dataset_pairwise_summary.pl`    | `summary_pairwise.py`


### Helper script changes**

Each former .pm file will be its own .py file in the `src` folder.

_'PRIMUS' package_

Perl module name                  | Python script name
---                               | ---
`get_age_flags.pm`                | `flags.py`
`compare_fam_files.pm`            | `compare.py`
`imus.pm`                         | `imus.py`
`PRIMUS_plus_ERSA.pm`             | `primus_ersa.py`
`reconstruct_pedigree_v7.pm`      | `reconstruct.py`
`prePRIMUS_pipeline_v7.pm`        | `preprimus.py`
`predict_relationships_2d.pm`     | `relationships.py`
`node_v7.pm`                      | `node.py`

_'File' package_

Perl module name                | Python package name
---                             | ---
File::Spec                      | pyinstaller
File::Basename                  | os.path.basename
File::CheckTree                 | os.walk
File::Compare                   | filecmp
File::Copy                      | copyfile    
File::DosGlob                   | fnmatch    
File::Fetch                     | filecmp
File::Find                      | os.walk & os.path
File::Path                      | os.path
File::stat                      | stat
File::Temp                      | tempfile

_'Getopt' package_

Perl module name                | Python package name
---                             | ---
Getopt::Long                    | argparse

_'Statistics' package_

Perl module name                | Python package name
---                             | ---
Statistics::Distributions       | statistics


### Specific Python conversion examples (of Perl conventions)


Objective | Perl method | Python method
--- | --- | ---
Checking if a file exists (across multiple directories) | `-e $filename` or `-e $filepath` | call `filenamematch('filename')` from `tools` folder


## Conceptual Recoding

### Calling a script (not a function) with arguments

In Perl (v1.9.0), primus_kickoff7.pl makes some calls to other main scripts and passes certain arguments in the process, then these scripts use shift to grab the responses before running the rest. (ex. results_dir / output_dir and dataset_name). The Python version will take these as arguments through argparse instead of arguments in a function since they are not being passed into a specific function in the script; rather, they are being used in the code that is run at the beginning of the [Python] file prior to any functions being touched.

---

## Installation Requirements


- .bed/.bim/.fam files for input into the PRIMUS pipeline
    - Currently, directories to these data inputs are initialized in `config.json` as "prePLINK_data", "1st_degree_networks", and "2nd_degree_networks". 
- [PLINK v1.9.x](https://www.cog-genomics.org/plink/1.9/)
- [Python3](https://www.python.org/downloads/)
- Python requirements : see `requirements.txt` or download from the command line

    ```
    pip install -r requirements.txt
    ```


## FAQ

1. *How do you address the scenario of having individuals in the 'bad' network that are actually related to the people in the good networks that have been shunted away (since they are removed and reconstructed separately from the individuals in the bad network)?*

    Most bias has the effect of overestimating relationship, not vice versa, so this is largely not a cause for concern re: results accuracy. 

2. *If you remove 1 ‘bad egg’ from the clump networks, does it disperse the rest of the individuals into adequate-sized networks, or do you sacrifice identifying any potential relationship between these specific individuals?*

    TBA = think of a way to test this - make an example script



## Licensing

TBD ...


