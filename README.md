# Context

All contents in this repo (except for this `README.md`) is just
the `osx-64` folder from:

https://github.com/touilleMan/godot-python-assetlib-repo/tree/master/addons/pythonscript

touilleMan's GitHub page for the project is here:

https://github.com/touilleMan/godot-python

See https://github.com/sustainablelab/pythonscript-windows-64 for
a similar repo copy of the `windows-64` folder for Windows users.

See https://github.com/sustainablelab/macos-setup.git for help
setting up for using git from the Terminal.

## Getting this the normal way

Normally this is installed by opening Godot and searching for
`pythonscript` in the **AssetLib**. And that is how I obtained
this copy, version 0.50.0.

## For collaborators on Windows and macOS

So why do I need this copy -- what's wrong with installing
through Godot? My collaborators are Windows and macOS users and
they do not use Godot. I created this copy to make it easier for
them to run my Godot projects.

Ideally, I'd just give them a Windows or macOS executable.
Unfortunately, project export does not yet work on Windows and
macOS for projects that use the pythonscript addon. Collaborators
run my project by cloning my project, downloading Godot, then
making an alias to execute Godot on my project's project.godot
file. That much is reasonable. But asking people to also setup
pythonscript and do the pip install steps is a bit much.

So, instead of walking folks through downloading pythonscript via
the Godot AssetLib and doing the pip install steps, I came up
with this workaround:

- I make this repo a git submodule in my Godot project
- I tell my collaborators:
    - to `git clone` the project
    - and to run the two `git submodule` commands that copy this
      repo (and my other submodules) into their local repo

# Steps for making a new project

Create a Godot project. Copy the `pythonscript.gdnlib` into the
project top level. Here it is:

```
[general]

singleton=true
load_once=true
symbol_prefix="godot_"

[entry]

OSX.64="res://addons/pythonscript/osx-64/libpythonscript.dylib"

[dependencies]

OSX.64=[]
```

Also create an `addons` folder at the top level:

```bash
% mkdir addons
```

Add this repo as a submodule:

```bash
% git submodule add https://github.com/sustainablelab/pythonscript-osx-64.git addons/pythonscript
```

*Notice the above command also puts the repo in the correct
folder.*

The directory structure should now be:

```
project-folder/
    pythonscript.gdnlib
    addons/
        SUBMODULE GOES HERE IN A FOLDER NAMED: pythonscript/
            osx-64/
    (other top-level files and folders)
```

*This mimics the structure when installed through the Godot
AssetLib.*

I treat Python packages I am developing for use in the project in
the same way. I add the Python package as a submodule in the
addons folder. I also install it in **editable** mode (with
`-e`). For those unfamiliar, the python.exe *used by
pythonscript* comes with no packages except for the `godot`
package and you pip install whatever you need.

```
project-folder/
    addons/
        pythonscript/
            osx-64/
                bin/
                    python3.8 <--- THIS PYTHON
```

Enter the bin folder:

```bash
% cd addons/pythonscript/osx-64/bin
```

I run `ls -l` and I see this python3.8 is **not executable**:

```shell
% ls -l ./python3.8
-rw-r--r--  1 sustainablelab  staff  8816 Mar 30 13:20 python3.8
```

I need to make it executable before I can run it:

```shell
% chmod 744 ./python3.8
% ls -l ./python3.8
-rwxr--r--  1 sustainablelab  staff  8816 Mar 30 13:20 ./python3.8
```

Now this works:

```shell
% ./python3.8 -V
3.8.5
```

Install pip:

```bash
% ./python3.8 -m ensurepip
Looking in links: /var/folders/nf/h01s6fxx5dg4fg3xzcxg8sm80000gr/T/tmpnxv_t489
Processing /private/var/folders/nf/h01s6fxx5dg4fg3xzcxg8sm80000gr/T/tmpnxv_t489/setuptools-47.1.0-py3-none-any.whl
Processing /private/var/folders/nf/h01s6fxx5dg4fg3xzcxg8sm80000gr/T/tmpnxv_t489/pip-20.1.1-py2.py3-none-any.whl
Installing collected packages: setuptools, pip
Successfully installed pip-20.1.1 setuptools-47.1.0
```

Update pip:

```bash
% ./python3.8 -m pip install -U pip
Collecting pip
  Using cached pip-21.0.1-py3-none-any.whl (1.5 MB)
Installing collected packages: pip
  Attempting uninstall: pip
    Found existing installation: pip 20.1.1
    Uninstalling pip-20.1.1:
      Successfully uninstalled pip-20.1.1
Successfully installed pip-21.0.1
```

I pip install my development packages in **editable** mode so
that collaborators get updates to my Python package simply by
pulling the submodule changes, rather than having to remember how
to reinstall my package via pip. It also saves me time: pushing
to a GitHub repo is much faster than re-uploading to PyPI.

I add the package as a submodule in the `addons` folder and
install in editable mode. For example, I have a package named
`microspec`. I add it as a submodule in the `addons` folder. Then
I install it in editable mode (I'm still in the directory with
`python3.8`, ../../../ puts me in the `addons` folder):

```bash
% ./python3.8 -m pip install -e ../../../microspec
Obtaining file:///Users/sustainablelab/chromation/devkit-gui/addons/microspec
Collecting pyserial
  Using cached pyserial-3.5-py2.py3-none-any.whl (90 kB)
Installing collected packages: pyserial, microspec
  Running setup.py develop for microspec
Successfully installed microspec pyserial-3.5
```

# Steps for cloning an existing project

Clone the project like you would any other repo:

```bash
% git clone ...
```

The submodules are empty folders. Pull the submodules in with
these two commands:

```bash
% git submodule init
% git submodule update
```
