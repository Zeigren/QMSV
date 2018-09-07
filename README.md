# QMSV

The idea of QMSV is to make Intel/Altera FPGA development outside of the Quartus/ModelSim GUIs somewhat easier, hopefully while making some improvements. It uses [Sublime Text 3](https://www.sublimetext.com) with the [Sublime System Verilog](https://sv-doc.readthedocs.io/en/latest/) package to improve the actual HDL writing experience. Quartus is used for all the typical stuff, and ModelSim for linting. [VUnit](https://vunit.github.io/index.html) is used as a unit testing framework, in this case for ModelSim which runs any tests or simulations.

QMSV contains build systems with syntax highlighting and in line errors* for Quartus, ModelSim, and VUnit. As well as a snippet for a python script that is used for generating and updating Quartus project files, it also creates the basic folder structure, a VUnit python script, a Sublime Text project file, and a .gitignore file. There is also a included snippet to create GPL v3 headers in your SystemVerilog or Verilog files.

QMSV is currently designed for use in Windows.

*in line errors only for Quartus and ModelSim

## Installation

- [Python](https://www.python.org/downloads/) 3.7.x
-  [Sublime Text 3](https://www.sublimetext.com/3)
- [Install Package Control](https://packagecontrol.io/installation)
- Install Sublime System Verilog from Package Control(ctrl+shift+p and type Package Control Install Package and search for it)
- Install [VUnit](https://vunit.github.io/index.html)
    `python pip install vunit_hdl`
- Create an environment variable QUARTUS_ROOTDIR at C:/intelfpga_lite/18.0 or equivalent
- Download the QMSV files and put them in a folder called QMSV in your Sublime Text 3 Packages folder (Preferences>Browse Packages...)
- Have [Quartus + ModelSim](https://www.intel.com/content/www/us/en/software/programmable/quartus-prime/download.html) installed

## Initialization

- Create a folder and open it in SublimeText
- Create a quartusregen.py file and save it (name doesn't actually matter) type in QuartusRegen and hit enter/tab
- Tab through and change the basic variables for Quartus, defaults to MAX10 10M08SCE144C8G with 3.3v everything
- Build it (ctrl+b), it'll generate the Quartus Project files, a basic test.py for VUnit, a src/topfile.sv, a src/test/tb_topfile.sv, a Sublime Text project file, and a work folder.

Any HDL or other files that are to be included in the Quartus project should be put into the 'src' folder, all test benches go into the 'src/test' folder and should be named 'tb_\*.\*' or '\*._tb.\*'. Supported file types in the test folder will be added to Quartus as testbench files.

Besides creating the Quartus project quartusregen.py is also used to update it. Anytime files are added or removed run it to update the .qsf file.

The test.py file is used to run VUnit for any tests or simulations, it is created with a basic template that can be used right off the bat.

The Sublime Text project file is used to manage project specific options and settings. It is also the easiest way to open up a project in Sublime Text, just double click on it and you're good to go.

The work folder is just there to appease ModelSim, currently it won't _work_ without it.

## Trying Out QMSV

A good project to test QMSV with is the [VUnit verilog uart example](https://github.com/VUnit/vunit/tree/master/examples/verilog/uart/src).

When using quartusregen.py just change the first four variables to:
- topfile = "uart_rx"
- tb_topfile = "tb_uart_rx"
- topfile_lib = "uart_lib"
- tb_topfile_lib = "tb_uart_lib" 

Download the example and put the four files into the 'src' and 'src/test' folders, and that's it!

## Basic Usage

### ModelSim Vlog

There is a build system for linting using ModelSim Vlog. Just have a HDL file open [".sv", ".v", ".verilog", ".vhd", ".vhdl"] then either select to build using ModelSimVlog under Tools>Build System or press ctrl+shift+b and select it from the drop down menu. It might fail the first time you run it, but just run it again. Build Results should have some highlighting and errors/warnings should appear in line, use F4 and shift+F4 to cycle through those.

### Quartus

With the *.qpf file or topfile open select to build using Quartus under Tools>Build System or press ctrl+shift+b and select it from the drop down menu. There are several options to choose from, the first option "Quartus" runs everything except clean. It can take a bit before text appears in the Build Results. Build Results should have some highlighting and errors/warnings should appear in line, use F4 and shift+F4 to cycle through those. Most Quartus files will appear in a output_files folder, but it also creates a db and incremental_db folder.

### VUnit

VUnit runs all simulations and unit tests through ModelSim. With an HDL file open [".sv", ".v", ".verilog", ".vhd", ".vhdl"] select to build using VUnitCLI under Tools>Build System or press ctrl+shift+b and select it from the drop down menu. There are several options to choose from, the first option "VUnitCLI" runs the test.py file as is. Build Results should have some highlighting. Any generated VUnit files will appear in a vunit_out folder.

### Snippets

Type 'gpl_sv' into any document and it'll generate a GPL v3 header, which has been commented out for use in SystemVerilog/Verilog files. Tab through and enter in the appropriate bits.

### Sublime Text Project

[Sublime Text Projects](https://www.sublimetext.com/docs/3/projects.html) add some additional useful functionality, besides making it easier to open up a project. You can specify project specific settings and build systems, as well as choose what folders or files to include in the sidebar.

## More Info

### ModelSim Vlog

You can find more ModelSim Vlog commands [here](https://www.microsemi.com/document-portal/doc_view/136660-modelsim-me-10-5c-reference-manual-for-libero-soc-v11-8) (pdf), which you can add to the ModelsimVlog.sublime-build file as [variants](https://www.sublimetext.com/docs/3/build_systems.html#options) or in the [Sublime Text project file](https://www.sublimetext.com/docs/3/projects.html).

### Quartus

Here are what the various build options do

Quartus
- Does everything

Analysis & Synthesis - quartus_map
- Creates a project if one does not already exist, and then creates the project database, synthesizes your design, and performs technology mapping on design files of the project.

Fitter - quartus_fit
- Places and routes a design. Analysis & Synthesis must be run successfully before running the Fitter.

Timing Analyzer - quartus_sta
- Performs ASIC-style timing analysis of the circuit using constraints entered in Synopsys Design Constraint format.

Assembler - quartus_asm
- Creates one or more programming files for programming or configuring the target device. The Fitter must be run successfully before running the Assembler.

EDA Netlist - quartus_eda
- Generates netlist files and other output files for use with other EDA tools. Analysis & Synthesis, the Fitter, or the Timing Analyzer must be run successfully before running the EDA Netlist Writer, depending on the options used.

Power Analyzer - quartus_pow
- Analyzes and estimates total dynamic and static power consumed by a design. Computes toggle rates and static probabilities for output signals. The Fitter must be run successfully before running the PowerPlay Power Analyzer.

Clean
- Deletes incremental_db folder, and removes most of the files from the db and output_files folders.

You can find more Quartus commands [here](https://documentation.altera.com/#/link/mwh1410471376527/mwh1410470998554), which you can add to the Quartus.sublime-build file as [variants](https://www.sublimetext.com/docs/3/build_systems.html#options) or in the [Sublime Text project file](https://www.sublimetext.com/docs/3/projects.html).

### VUnit

The VUnit build system simply uses some of the CLI arguments. List of the VUnit CLI arguments and what they do can be found [here](https://vunit.github.io/cli.html), if you want to add more they can be added to the VUnitCLI.sublime-build file as [variants](https://www.sublimetext.com/docs/3/build_systems.html#options) or in the [Sublime Text project file](https://www.sublimetext.com/docs/3/projects.html).