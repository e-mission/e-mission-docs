# How to Debug e-mission-server

This is a guide that explains how to debug and step through the e-mission-server code

## Prerequisites:
- [Must have e-mission-server installed and running](https://github.com/e-mission/e-mission-server#installation)
- Must have Visual Studio Code installed which will act as the debugger
- Basic knowledge of how to use Jupyter Notebook `.ipynb` files (how to run a cell/ how to create a cell/ etc.)
- Basic knowledge of how to debug code (how to step over/ step in/ step out/ set watch variables/ etc.)

## Setup:
- Download the debug file `clipboard.ipynb` from https://github.com/sebastianbarry/e-mission-server/blob/debugger-notebook-file/clipboard.ipynb
- Move this file into your `/e-mission-server` root directory

## How-to:
1. Open `clipboard.ipynb` in Visual Studio Code
2. Set the Visual Studio Code environment to the `emission` conda environment
<img src="/Users/sbarry/Documents/GitHub/e-mission-docs/docs/assets/e-mission-both/select_emission_environment_in_vscode.png" />
    * _If the emission conda environment isn't an option, you first may need to activate the environment in a terminal: https://github.com/e-mission/e-mission-docs/blob/master/docs/install/manual_install.md#python-distribution_
3. Run the first cell of `clipboard.ipynb`. The first cell contains all the necessary imports for the rest of the cells in the notebook.
4. Set Visual Studio Code breakpoints on the lines that you wish to debug within the `e-mission-server` code.
5. Run any of the cells from `clipboard.ipynb` in debug mode _(click the drop down next to the "Run Cell" button and select "Debug Cell")_ that you wish to debug. The code will run within Visual Studio Code and hit any of the breakpoints you've set, allowing you to step through the code, see/set watch variables, etc. 
    * **_How does this work?_**: Because we are executing the e-misison-server functions directly from within Visual Studio Code (as opposed to the terminal), VSCode is the medium which is executing the server code and thus will land on any breakpoints set.
    * **_Can this work with scripts executed with the Terminal?_**: No, this method will only work if you execute an e-mission-server Python function within VSCode, which is what we are accomplishing in the example `clipboard.ipynb` file linked above

## How do I create my own cells for debugging?
There are already a few cells found within the linked `clipboard.ipynb` file above that complete some tasks _(i.e. displaying the database entries, loading data and running the pipeline for a specific user, resetting the pipeline, etc.)_, but what if we want to create our own cell for debugging?

1. First, you must import the `.py` files that contain the e-mission-server Python functions you wish to execute
    * _Typically, we run scripts in the Terminal which import e-mission-server files and run their Python functions. THIS method involves us doing the same thing those scripts do, but in a Jupyter notebook rather than a Terminal script. Because of this, we must import the e-mission-server files that contain those functions we wish to execute and debug_
2. Then, you can use the imported files to run any functrions from within
3. Run and test to make sure the cell works as intended