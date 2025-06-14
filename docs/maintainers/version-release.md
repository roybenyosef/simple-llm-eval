# Version Release 🚀

!!! info "Versioning Schema"
    simpleval versioning follows [Semantic Versioning](https://semver.org/).

    **Stable versions**

    `MAJOR.MINOR.PATCH` where:
    
    - `MAJOR` version when you make incompatible or breaking changes
    - `MINOR` version when you add functionality in a backwards-compatible manner
    - `PATCH` version when you make backwards-compatible bug fixes and minor changes

    Examples: `1.0.0`, `1.0.1`, `2.0.0`

    **Pre-release versions**

    For pre-release versions, append `-alpha.x`, `-beta.x`, or `-rc.x` (only those three are allowed),
    where alpha is for early testing, beta is for more stable testing, and rc is for release candidate.

    Examples: `1.0.0-alpha.1`, `1.0.0-beta.1`, `1.0.0-rc.1`

## Release Procedure

### Release Automation

*[version-bump]: Follow Semantic Versioning.<br>To help determine patch/minor/major version bump, run: `ci/scripts/inspect_changes_since_last_release.sh` to view changes since the last release.

!!! info "Automatic Release Procedure (Recommended)"
    - [ ] Run the [Init Release Workflow](https://github.com/cyberark/simple-llm-eval/actions/workflows/init-release.yml){target="_blank"} workflow and wait for it to complete. (You will need to decide how to version-bump)

    ???- note "Init Release Workflow - Under the Hood"
        The *Init Release Workflow* will:

        - Creates a release branch named `release/auto-next-version` (or provide your own branch name)
        - Bump patch/minor/major or specific version (set `version_bump` and optionally `version`)
        - Update `CHANGELOG.md` for this release
        - Create a pull request with the changes
    
    - [ ] Review the pull request created by the workflow
    - [ ] Optionally tweak `CHANGELOG.md` to your liking
    - [ ] Merge the pull request to main - This will trigger the tag creation workflow
    - [ ] Wait for the [version tag creation workflow](https://github.com/cyberark/simple-llm-eval/actions/workflows/create-tags-for-release.yml){target="_blank"} to complete
    - [ ] Trigger the [Release Workflow](https://github.com/cyberark/simple-llm-eval/actions/workflows/release.yml){target="_blank"} 
    - [ ] Manually publish the release to PyPI
    ???- note "Release Workflow - Under the Hood"

        Since operations done with internal tokens will not by default trigger workflows, the [Release](https://github.com/cyberark/simple-llm-eval/actions/workflows/ci.yml) workflow is triggered manually.

        The release workflow will:
            
        - Validate the `pyproject.toml` version against the tag name
        - Build the package
        - Create GitHub release
        - Attach binaries to GitHub release
        - Publish the docs in case of release version (pre-release versions will not publish the docs)

## 📚 Docs Only Release
The release workflow will deploy the docs automatically for stable releases, so if you want to publish the docs for a pre-release version, you can do so by running the [Publish Docs Workflow](https://github.com/cyberark/simple-llm-eval/actions/workflows/docs-release.yml)

To learn more about the docs publishing, see [Publishing the Docs](../developers/dev-notes.md/#publishing-the-docs)).

**NOTE: Docs publishing is done only for the top level version (release or pre-release)**


## 💀 Obsolete Release Procedure - Go No Further 💀

## Run the Release Procedure with Scripts (Not Recommended)

!!! info "Using the Release Scripts"
    - [ ] Run the release procedure script in one of the following ways:
    
    ```
    ./ci/scripts/create_version_pr.py --version 1.0.0-rc5g # Set a specific version (good for pre-release)
    ./ci/scripts/create_version_pr.py --bump-patch
    ./ci/scripts/create_version_pr.py --bump-minor
    ./ci/scripts/create_version_pr.py --bump-major
    ```

    - [ ] Manually open the release notes, click on "generate release notes" and update them as needed
    - [ ] After the release workflow is complete, update the `CHANGELOG.md` file with the new version and the changes made in this release (take from the release notes)
    - [ ] Optionally review and update the release note in the release
    - [ ] Publish the package to PyPI

## ❌ The Manual Way (Even less Recommended)

### 1. Update Version in `pyproject.toml`

!!! info "Recommended: Run the create version PR script"
    ```
    ./ci/scripts/create_version_pr.py
    ```

Otherwise follow these steps:

* Update `pyproject.toml`: Using `uv` Update the version according to the versioning schema above. Make sure you adhere to the versioning rules and only include alpha, beta, or rc for pre-release versions.

examples:
```
uv version --bump patch # 1.0.1 -> 1.0.2
uv version --bump minor # 1.0.1 -> 1.1.0
uv version --bump major # 1.0.1 -> 2.0.0

uv version 1.0.1-alpha.1 # set to pre-release version
uv version 1.0.1-beta.1 # set to pre-release version
uv version 1.0.1-rc.1 # set to pre-release version

```

* Run `uv sync` to update `uv.lock`.

* Create a pull request for the changes and merge to main.

### 2. Create a Release Tag
On the main branch, create a tag using the `ci/scripts/create_tag.py` script. The script will read the version from `pyproject.toml`
and create a tag with the correct format.

### 3. Update the Release Notes

* Wait for the [release workflow](https://github.com/cyberark/simple-llm-eval/actions/workflows/release.yml) to end successfully
* Manually open the release notes, click on "generate release notes" and update them as needed
* In the future this will be automated

### 4. Update the Changelog

* After the release workflow is complete, update the `CHANGELOG.md` file with the new version and the changes made in this release (take from the release notes).

### 5. Run the Publish Pipeline
Do it

<br>
