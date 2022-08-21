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

### Secure Python packaging and release using GitHub Continuous Deployment

This is the final stage where a couple of experiments will be performed to showcase
the different methods you can use to securely build and release your project
through GitHub Continuous Deployment.
Here are the steps you will need to do:
1. You will have to generate a token responsible exclusively for your new project.
This is done so that you don't have to share your Test PyPI password with
GitHub. You can generate a new token by going on Test PyPI and clicking
`<YOUR_USERNAME>` -> `Your Projects` -> `left of your project name "Manage"` -> `Settings`
-> `Generate token for <PROJECT_NAME>`. You want to make sure you click on the `Scope`
menu that the token is responsible only for your project.
2. When you generate the new token **SAVE** it somewhere as it will be needed later.
3. Create the following directories: `.github/workflows`.
4. Inside `.github/workflows` create `release.yml`
5. Add your first workflow (the content is inside the commit of these changes).
6. On the GitHub page of your project go to `Actions` and below you can see the
`Workflows` menu. There you will see the `GitHub release workflow example` workflow
and when you click it on the right you can click `Run workflow` which will trigger
the workflow.
7. Now we will add steps to setup python, install our build dependency, build
our project and list the sha256 sums of the resulted artifacts. The new changes
in the `release.yml` are in the commit containing this change.
8. Again run the workflow (as we did on step 6). Then click the `build` job and
click the `List shas` step. Next build your project locally, list the sha256 sums
of the newly generated build files and verify that locally listed sha sums are
the same sha256 sums as the listed on GitHub.
9. Now you should create an `environment` for your project. You can do this by
going to your project's GitHub page, clicking `Settings` -> `Environments`
-> `New environment`. Give whatever name you want to it.
10. Add a secret to the environment. You have to add the newly created token
responsible for your project which you created at `Upload the project to Test PyPI`
stage step `1`. The name of that secret has strict rules, so I suggest you just
call it `PYPI_TOKEN`. By making the secret bound to the environment this means
that the access rules to that secret are limited by the environment and cannot
be easily retrieved.
11. On the same page under `Environment protection rules` click `Required
reviewers and in the text field add your GitHub username and click
`Save protection rules`. This will make sure that before a GitHub workflow has
access to your environmental secrets you have to manually approve that.
12. In the `release.yml` file add a new step in the `build` job making the output
from the workflow available to other jobs. The changes in the `release.yml` can
be seen in the commit containing that change.
13. Add a new `upload` job with steps to `Fetch build artifacts` and
`Publish on PyPi`. The changes in the `release.yml` can
be seen in the commit containing that change.
14. Make a new release by updating the project version in `pyproject.toml`.
15. Tag your local project with `git tag "<TAG_NAME>" -m <TAG_MESSAGE>"`
16. Push the changes to GitHub. Don't forget to add `--tags` to the push command
otherwise the tag won't appear on GitHub.
17. Run the workflow manually (as described in step 6), verify sha256 sums (as
described in step 8) and if everything is correct go to your project's GitHub page
`Actions` -> choose the last workflow run -> you will see that `upload` is orange, click it ->
choose `Review pending deployments` -> click `Release` -> click `Approve and deploy`.
Now you can verify everything is as expected by going to [Test PyPI](https://test.pypi.org/).
