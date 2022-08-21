# oss-eu-2022-tutorial-mv
Repository for the OSS Europe 2022 tutorial on Secure Python packaging and
release using Continuous Deployment.

## Tutorial goals

There are a couple of main goals in this tutorial:
- showcase how Python packaging can and release can be done securely with CD
through GitHub
- at all stages of the tutorial showcase make sure all builds and releases of
this example project are reproducible.

## Tutorial stages
There are three main stages of this tutorial:

1. Initialize example project
2. Upload example project to Test PYPI
3. Package and release the example project using CD provided by GitHub

There will be instructions for each of the stages.

### Initialize example project

Here are the steps you should follow at this stage of the tutorial:
1. Choose a name for your new GitHub project. Give it a name that can be used as a
unique identifier in a global scope (that is if you want your python project
name to be the same as the GitHub project title). For example, you can use
the format I used: `oss-eu-2022-tutorial-<YOUR_GITHUB_USERNAME>`
1. Create a **public** GitHub project with the name of your choice. It should
contain a `README.md` file and you should add a `LICENCE` for your project.
3. Clone the repository locally.
4. Create a virtual environment with the python version of your choice.
5. Add a `.gitignore` file ignoring the virtual environment folder.
6. Create an example code. NOTE: Don't add any dependencies to your code as
later you will publish and install your python project from Test PyPI and you
can make the mistake to install dependencies from Test PyPI which is dangerous.
The only allowed dependencies are those that won't be destributed with the
package.
7. Create `pyproject.toml` configuration file and populate it the way as
suggested in [Packaging Python Projects — Python Packaging User Guide](https://packaging.python.org/en/latest/tutorials/packaging-projects/).
8. Make sure that inside `pyproject.toml` you will update:
- `name` and `autors` fields under the `project`section
- the `project.urls` section with the actual GitHub project URLs.
- the `licence` field to
`licence = { text = { <LICENCE_NAME> }` where the `<LICENCE_NAME>` should be the
name of your licence. This is done so that your project license field can look
better on Test PyPI.
- the `build-system` section by pinning `hatchling` version in the `requires` with
the version you are going to use. **This is important for reproducibility reasons.**
9.  Add a short explanation to your README and if you haven't yet commit your
changes.

### Upload the project to Test PyPI

At this stage you are going to build your python project and upload it to Test PyPI.
Here are the steps you should follow at this stage of the tutorial:
1. Add a `requirements.txt` file containing the requirements needed to build
and upload our project (you can see its content in the commit containing that
change).
2. Install the dependencies from `requirements.txt` with `pip install -r requirements.txt`
3. Generate distribution archives by building your project with `python3 -m build`
It's possible that it will require you to install `virtualenv`.
4. Add the `dist` directory to `.gitignore`.
5. Create an account on [Test PyPI](https://test.pypi.org/).
6. Upload your project to Test PyPI with `python3 -m twine upload --repository testpypi dist/*`
7. You can verify that your project was indeed uploaded to Test PyPI by looking
into your profile and you can also install it with the command
`python3 -m pip install --index-url https://test.pypi.org/simple/ --no-deps <PROJECT_NAME>`.
