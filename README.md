# MKE 4k Documentation

The `mke-docs` repository contains the MKE 4k documentation content and all
the assets that are required to build the [MKE 4k documentation website](https://mirantis.github.io/mke-docs/),
which uses Hugo with the [Hextra theme](https://imfing.github.io/hextra/).
Currently, the docs are published using GitHub actions on GitHub pages from the `main` branch.

To build and preview MKE 4k documentation:

1. Install [Hugo](https://gohugo.io/installation/) on your local system.

2. Start the Hugo server from within your local `mke-docs` repository:

    ```bash
    hugo server
    ```

3. View the local documentation build at http://localhost:1313/mke-docs/.

## Making changes to the docs

All documentation changes should be done in the `dev` folder. When a release is created,
these changes will be used to create the new release pages.

Some exceptions to this might be:
- Mention a bug found in version 1.2.3
- Add a work around for something in version 1.2.3
- Fix a typo, grammer, etc

## Releases

Each of the folders in the `content/` directory represents a version that will
be shown in the version dropdown of the page. The `dev` version is hidden in the
dropdown but it can be navigated to by replacing the version in your URL with `dev`.

The `dev` directory is the docs changes for the upcoming release. When a release
is made, a copy is made of this directory and ranamed to the new version. Any
doc changes should always be done to the `dev` folder unless it is specific
for a release.

### Creating a release
You should first switch to a new branch for creating a release. This will only be
for opening a PR to merge the changes in. The branch does not need to be (and should not be) preserved
once the changes are merged into main.

The following script will make all the changes for a new release:
```
./scripts/new_release <new release version>
```

Example:
```
./scripts/new_release 4.1.1
```

This script will:
- Use the latest changes in the `dev` folder to create the new release (copy dev)
  **No changes will be made to the `dev` folder.**
- Remove redirects for `latest` from the old release and add them to the new release
` Remove redirects for `docs from the old release` and add them to the new release (this is for backwards compatibility from the previous folder structure)

Look them over for any issues and then push up the changes to github and create a PR to be
merged into main.

Once the PR is merged into main, a tag should be placed on the commit the same as the version name.
