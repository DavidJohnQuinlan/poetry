### poetry
Poetry helps you declare, manage and install dependencies of Python projects,
ensuring you have the right stack everywhere. It allows you to declare the
libraries your project depends on and it will manage (install/update) them
for you.

### Installation and configuration
It is possible to install Poetry with Brew using the following command:
`brew install poetry`.

Once installed one can enable tab auto-completion for Poetry within Oh-My-Zsh by 
typing the following commands in the terminal:

```
mkdir $ZSH_CUSTOM/plugins/poetry
poetry completions zsh > $ZSH_CUSTOM/plugins/poetry/_poetry
```

Finally, add `poetry` to the plugins function in the `~/.zshrc` profile, i.e.
```
plugins(
    poetry
    )
```

The `pyproject.toml` file is used to configure Poetry for a specific project. Poetry 
uses pyproject.toml to replace the setup.py, requirements.txt, setup.cfg and the 
newly added Pipfile. An example `pyproject.toml` configuration file is shown below:

```
[tool.poetry]
name = "my-package"
version = "0.1.0"
description = "The description of the package"
license = "MIT"
authors = ["Sébastien Eustace <sebastien@eustace.io>"]

# Markdown files are supported
readme = 'README.md'

repository = "https://github.com/python-poetry/poetry"
homepage = "https://github.com/python-poetry/poetry"

keywords = ['packaging', 'poetry']

[tool.poetry.dependencies]
python = "~2.7 || ^3.2"  # Compatible python versions must be declared here
toml = "^0.9"

# Dependencies with extras
requests = { version = "^2.13", extras = [ "security" ] }

# Python specific dependencies with prereleases allowed
pathlib2 = { version = "^2.2", python = "~2.7", allow-prereleases = true }

# Git dependencies
cleo = { git = "https://github.com/sdispater/cleo.git", branch = "master" }

# Optional dependencies (extras)
pendulum = { version = "^1.4", optional = true }

[tool.poetry.dev-dependencies]
pytest = "^3.0"
pytest-cov = "^2.4"

[tool.poetry.scripts]
my-script = 'my_package:main'
```   

Now that Poetry has been installed, it is possible to create a new project using the 
command: `poetry new project_name`. This creates a `project_name` directory with a 
basic project structure, i.e. it automatically creates a `README.rst`, a `tests` folder,
a `project_name` sub-folder and the poetry project configuration file `pyproject.toml`.

To use Poetry within an existing project use the command `poetry init` to initalise 
the project directory. Poetry runs through a list of questions to gather information
related to the current project. This information is subsequently used to create the 
custom `pyproject.toml` configuration file. 

To add dependencies (also possible to add dependencies during `poetry init` 
questionnaire!) use the following command:
`poetry add pandas`
This command automatically finds a suitable version constraint and installs the 
package and subdependancies. It also adds the package requirement details in the 
`pyproject.toml` configuration file in addition to adding it to the `poetry.lock` file.

Poetry automatically creates a virtual environment if one does not exist already. 
The addition or removal of any packages will be automatically accounted for by the 
virtual environment. If new dependencies are added, developers can refresh their 
environment using poetry install.

It is interesting to note that it is also possible to define developer-only 
dependencies can be added with the –dev argument. This means that Poetry will also 
manage developer dependancies like Black, isort or Flake8.

To run any scripts or tests you the following command structure:
`poetry run xxx`. For example to run a Python script use the command
`poetry run python my_script.py` or to execute Flake8 use: `poetry run flake8`.

To activate the virtual environment use the following command: 
`poetry shell`
This command creates a new shell within which you can operate. To exit the shell 
simply type `exit`.   


### Pre-commit Hooks
It is possible to install pre-commit hooks following the same method as before once 
`pre-commit` has been added as a developer dependency. The only differences are the 
commands used to install and run pre-commit, with these commands being pre-appended 
with the poetry syntax of `poetry run`.

### Poetry and Docker
Similarly, it is possible to integrate Poetry and a Dockerfile. Often Python packages 
need to be installed within a Docker container and therefore should be stated within
the Dockerfile, i.e. `RUN pip install -r requirements.txt`. However, given that 
Poetry removes the need for a requirements file, it is necessary to replace this 
process with:

```
# Install poetry:
RUN pip install poetry

# Copy in the config files:
COPY pyproject.toml poetry.lock ./

# Install only dependencies:
RUN poetry install --no-root --no-dev
``` 
However, any change to the versioning of your application the `pyproject.toml` 
changes thus requiring Docker to re-build the image again rather than using previously 
cached dependencies. This process will slow down the speed of the docker image builds 
which might be a pain of continuously updating the application. 