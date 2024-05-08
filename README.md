# CANFAR Science Containers for POSSUM/VLASS

This repository hosts Docker container configurations for the VLASS (TODO) and POSSUM pipelines, optimized for deployment on the CANFAR Science Platform but more broadly employable as well. These containers are designed to facilitate scientific computing tasks in a reproducible and scalable manner. The repository is based on the original repository found [here](https://github.com/opencadc/science-containers/).

For POSSUM there are currently two Dockerfiles, which have identical software, except that the 'notebook' version installs jupyterlab, while the 'headless' version does not


## Usage

This repository utilizes GitHub workflows to automate the building and pushing of new containers every time a significant commit is made (see the Actions tab). It follows the [Semantic Versioning](https://semver.org/) convention MAJOR.MINOR.PATCH to label the containers.

The workflow process is as follows:

1. **Make Changes**: Make necessary changes or additions to the Dockerfiles or any other relevant files within the repository.

2. **Commit Changes**: Commit your changes with a message following the [Conventional Commit Message](https://www.conventionalcommits.org/en/v1.0.0/) format:
   - For a feature (MINOR VERSION UPDATE): `git commit -m "feat(optional scope): description"`
   - For a bugfix (PATCH VERSION UPDATE): `git commit -m "fix(optional scope): description"`
   - For a breaking change (!) (MAJOR VERSION UPDATE): `git commit -m "featorfix!(optional scope): description"`

3. **Push Changes**: Push your changes to the repository: `git push`.

4. **Check for PR**: After pushing changes, check whether a pull request has been successfully created. In Continuous Deployment (CD), the `releaseer` action will release a pull request, while the `builder` action will fail.

5. **Merge Pull Request**: Go to the pull request on the GitHub website and merge it.

6. **Verify Build**: After merging the pull request, the `builder` action should complete successfully, and the new version will be pushed to CANFAR.

7. **Update Locally**: To sync the changes locally, pull the changed log and `.toml` file: `git pull`.

8. **Label Images**: Finally, go to [images.canfar.net (harbour)](https://images.canfar.net) and label your images with the appropriate labels (e.g., `headless`, `notebook`) to ensure they can be accessed by users.

## TODO

- Add functionality to automatically add labels once CANFAR supports this feature.

## Contributing

Contributions to this repository are welcome! Please feel free to submit issues or pull requests for bug fixes, new features, or improvements.



## Additional resources

The official CANFAR User Documentation can be found at: [https://www.opencadc.org/science-containers.git/](https://www.opencadc.org/science-containers/)

### Platform Infrastructure
The repository for the platform service infrastructure and deployment configuration is at:  [https://github.com/opencadc/science-platform](https://github.com/opencadc/science-platform)

[<img src="canfar-logo.png" height="200" />](https://www.opencadc.org/science-containers/)
