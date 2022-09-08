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

### Secure Python packaging and release using GitHub Continuous Deployment

This is the final stage where a couple of experiments will be performed and upon
completion you will have a method to securely build and release your project
through GitHub Continuous Deployment. Here are the steps you will need to do:
1. You will have to generate a token responsible exclusively for your new project.
This is done so that you don't have to share your Test PyPI password with
GitHub. You can generate a new token by going on Test PyPI and clicking
`<YOUR_USERNAME>` -> `Your Projects` -> `left of your project name "Manage"` -> `Settings`
-> `Generate token for <PROJECT_NAME>`. You want to make sure you click on the `Scope`
menu that the token is responsible only for your project.
2. When you generate the new token **SAVE** it somewhere as it will be needed later.
3. Create the following directories: `.github/workflows`.
4. Inside `.github/workflows` create the `release.yml` file.
5. Add your first GitHub job inside `release.yml`. An example is available in
the commit containing this step.
6. To activate the workflow run you will have to make a release. You can do this
by updating the `version` field in `pyproject.toml`.
7. Tag your local project with git tag `<TAG_NAME>" -m <TAG_MESSAGE>` where
`TAG_NAME` MUST start with a `v`.
8. Push the changes to GitHub.
9. Push the tags by adding by running `git push` and adding the `--tags` option.
10. View the last workflow that was automatically triggered by going to your project
GitHub page and the click `Actions` -> click the name of your workflow and verify
that the output is as expected.
11. You will add steps to setup python, install your build dependencies, build
your project and list the sha256 sums of the resulting artifacts. The new changes
are in the commit containing this change.
12. Make a new release and push it by following steps `6` to `9`.
13. View the last workflow that was automatically triggered. Then click on the
`build` job and then click the `List shas` step. Next build your project locally,
list the sha256 sums of the newly generated build files and verify that locally
listed sha256 sums are the same sha256 sums as the ones listed on GitHub.
14. For the next steps, you will need an `environment` for your project.
You can create one by going to your project's GitHub page, clicking `Settings` ->
`Environments` -> `New environment` and give whatever name you want.
15. Add a secret to the environment. You have to add the newly created token
responsible for your project which you created at `Upload the project to Test PyPI`
stage step `1`. The name of that secret has strict rules, so I suggest you just
call it `PYPI_TOKEN`. By making the secret bound to the environment this means
that the access rules to that secret are limited by the environment and the secret
cannot be easily retrieved.
16. You have to make sure that not all GitHub branches have access to your
secret. Go to the GitHub page of your project -> click `Settings` -> `Environments`
-> click the name of your environment -> under `Deployment branches` click the
`All branches` button and then choose `Selected branches` -> click
`Add deployment branch rule` -> in the `Branch name pattern` box write `v*`.
This makes sure that only branches/tags starting with the name `v` can use the
environmental secrets.
17. On the same page under `Environment protection rules` click `Required reviewers`
and in the text field add your GitHub username and click `Save protection rules`.
This will make sure that before a GitHub workflow has access to your environmental
secrets you will have to manually approve that.
18. In the `release.yml` file add a new step in the `build` job making the output
from the workflow available to other jobs.
19.  Add a new `upload` job with steps to `Fetch build artifacts` and
`Publish on PyPi`. Note: An example of the changes mentioned in steps 17 and 18
can be seen in the commit adding those steps.
20. Make a new release and push it by following steps `6` to `9`.
21. The workflow will be automatically triggered. After it finishes, verify
the sha256 sums (as described in step `12`) and if everything is correct at your
workflow page you will see that `upload` is orange, click it -> choose
`Review pending deployments` -> click `Release` -> click `Approve and deploy`.
Now you can verify everything is as expected by going to [Test PyPI](https://test.pypi.org/).

### Bonus content: add signing and release verification

You can go a step further and introduce signing and signature verification of your
releases. That will significantly improve the security of your project release
processes.
We have made the necessary changes introducing signatures and signing in the
last 5 commits.
Here are the steps you need to do in order to achieve that:
1. Add `sigstore` to your `requirements-build.txt` file.
2. Add the utility script we have added in the repository named `release`.
Note: you can use `sigstore` manually as a CLI tool, but for the purpose of
simplifying this tutorial we are going to use the `release` script.
3. Create a `signatures` directory at the root of your project.
4. Open `pyproject.toml` and add `/release` in the `tool.hatch.build.targets.sdist`
section.
5. Open `.github/workflows/release.yml` file and change the `List shas` step in
the `build` job to the `Verify release signature` step as shown in the previous commits.
6. Open `pyproject.toml` and bump the `version` attribute.
7. Build your project with `python3 -m build`
8. Sign your project by executing from the root folder: `./release sign {VERSION}`
where `VERSION` is the incremented version inside `pyproject.toml`.
9. Prepare all changes to be committed by adding them with `git add`.
10. Commit all changes.
11. Push the changes
12. Tag the changes. Remember the tag name MUST start with `v`.
13. Push the tags.
14. Go to your project's GitHub page. The workflow should be automatically
triggered and the `build` job should complete successfully. You can approve the
`upload` job and release the next version of your project.

This is all I wanted to show you in this tutorial!

This tutorial was developed with the great help of [Jussi Kukkonen](https://github.com/jku)
and [Joshua Lock](https://github.com/joshuagl)!
