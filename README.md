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

## Releases

Each of the folders in the `content/` directory represents a version that will
be shown in the version dropdown of the page. The `dev` version is hidden in the
dropdown but it can be navigated to by replacing the version in your URL with `dev`.

The `dev` directory is the docs changes for the upcoming release. When a release
is made, a copy is made of this directory and ranamed to the new version. Any
doc changes should always be done to the `dev` folder unless it is specific
for a release.

### Creating a release
The following script will make all the changes for a new release:
```
./scripts/new_release <new release number>
```

Example:
```
./scripts/new_release 4.1.1
```

Then commit the changes and open a PR to have it pushed up.
