## Tutorial stages
There are three main stages of this tutorial:

1. Initialize example project
2. Upload example project to Test PYPI
3. Package and release the example project using CD provided by GitHub

There will be instructions for each of the stages.
For each of the steps that include modification on the git repository, an example
will be given inside the commits describing those steps.
I won't mention explicitly where should you commit your changes. That is up to you.
The only places when that is required are when making a release with a tag.

### Initialize example project

Here are the steps you should follow at this stage of the tutorial:
1. Choose a name for your new GitHub project. Give it a name that can be used as a
unique identifier in a global scope (that is if you want your python project
name to be the same as your GitHub project title). For example, you can use
the format I used: `oss-eu-2022-tutorial-<YOUR_GITHUB_USERNAME>`
2. Create a **public** GitHub project with the name of your choice. It should
contain a `README.md` and `LICENCE` files.
3. Clone the repository locally.
4. Create a virtual environment with the python version of your choice.
5. Add a `.gitignore` file and add a line in it ignoring the virtual environment
folder.
6. Create the directories `src/<GITHUB_PROJECT_NAME>` and in it create
an `__init__.py` and a simple source file.
7. Create `pyproject.toml` configuration file and populate it the way
suggested in [Packaging Python Projects — Python Packaging User Guide: Configuring metadata](https://packaging.python.org/en/latest/tutorials/packaging-projects/#configuring-metadata).
8. Make sure that inside `pyproject.toml` you will update:
- `name` and `autors` fields under the `project`section.
Note: `name` and `GITHUB_PROJECT_NAME` must be the same.
- the `project.urls` section with the actual GitHub project URLs.
- the `licence` field to
`licence = { text = { <LICENCE_NAME> }` where the `<LICENCE_NAME>` should be the
name of your licence. This is done so that your project license field can look
better on Test PyPI.
- the `build-system` section by pinning `hatchling` version in the `requires` with
the version you are going to use. **This is important for reproducibility reasons.**
- add the `tool.hatch.build.targets.sdist` section with the `include` field where
you will describe which files and folders will be included in the build artifacts.
An example of all of those changes is included in the commit containing this step.
9. Add a short explanation to your README.

### Upload the project to Test PyPI

At this stage, you are going to build your python project and upload it to Test PyPI.
Here are the steps you need to follow:
1. Add a `requirements-build.txt` file containing the requirements needed to
build and upload our project. You can see an example in the commit containing
that change.
2. Install the build dependencies from `requirements-build.txt` with `pip install -r requirements-build.txt`.
3. Install `twine` with `pip install --upgrade twine`.
4. Generate distribution archives by building your project with `python3 -m build`
It's possible that you will have to install `virtualenv` with `pip install virtualenv`.
5. Add the `dist` directory to `.gitignore`.
6. Create an account on [Test PyPI](https://test.pypi.org/).
7. Upload your project to Test PyPI with `python3 -m twine upload --repository testpypi dist/*`
8. You can verify that your project was indeed uploaded to Test PyPI by looking
into your profile and installing it with:
`python3 -m pip install --index-url https://test.pypi.org/simple/ --no-deps <PROJECT_NAME>`.
