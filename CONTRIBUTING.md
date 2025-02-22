# Contributing

We welcome contributions! If you believe that a spec should be added, modified,
or removed from the list, consider submitting a pull request, taking the
considerations below into account. Alternatively, feel free to [raise an
issue](https://github.com/w3c/browser-specs/issues/new).

Please note the open source data and code [license](LICENSE.md) for this
project.


## Table of Contents

- [How to add/update/delete a spec](#how-to-addupdatedelete-a-spec)
  - [Pre-requisites](#pre-requisites)
  - [The actual list is in `specs.json`](#the-actual-list-is-in-specsjson)
  - [Compact form preferred](#compact-form-preferred)
  - [No `index.json` in the pull request](#no-indexjson-in-the-pull-request)
  - [Lint before push](#lint-before-push)
  - [Check before push](#check-before-push)


## How to add/update/delete a spec

If you believe that a spec should be added, modified, or removed from the list,
consider submitting a pull request, taking the considerations below into
account. Alternatively, feel free to [raise an
issue](https://github.com/w3c/browser-specs/issues/new).

### Pre-requisites

To prepare a pull request, please:
- check the [Spec selection criteria](README.md#spec-selection-criteria),
- install [Node.js](https://nodejs.org/en/) if not already done,
- fork this Git repository,
- install dependencies through a call to `npm install`


### The actual list is in `specs.json`

In practice, the `index.json` file is automatically generated by processing the
`specs.json` file, which thus contains the actual list. In other words, all
proposed changes must be made against the `specs.json` file, **do not edit the
`index.json` file directly**.

The `specs.json` is essentially a sorted (A-Z order) list of URLs. In most
cases, to propose a new spec, all you have to do is insert its versioned URL
(see [`url`](README.md#url) in README) at the right position in the list.

In some cases, you may need to go beyond a simple URL because the spec does not
follow usual rules and the code cannot compute the right information as a
result. A spec entry in the list may also be an object with the following
properties:

- `url`: same as the [`url`](README.md#url) property in `index.json`.
- `shortname`: same as the [`shortname`](README.md#shortname) property in
`index.json`. When the `forkOf` property is also set, note that the actual
shortname in the final list will be prefixed by the shortname of the base
spec (as in: `${forkOf}-fork-${shortname}`).
- `forkOf`: same as the [`forkOf`](README.md#forkof) property in `index.json`.
No need to set `seriesComposition` to `"fork"` when this property is set, the
build logic will take care of that automatically.
- `series`: same as the [`series`](README.md#series) property in `index.json`,
but note the `currentSpecification` property will be ignored.
- `seriesVersion`: same as the [`seriesVersion`](README.md#seriesversion)
property in `index.json`.
- `seriesComposition`: same as the [`seriesComposition`](README.md#seriesComposition)
property in `index.json`. The property must only be set for delta spec, since
full is the default and fork specs are identified through the `forkOf` property
in `specs.json`.
- `organization`: same as the [`organization`](README.md#organization) property
in `index.json` to specify the name of the organization that owns the spec.
- `groups`: same as the [`groups`](README.md#groups) property in `index.json`
to specify the list of groups that develop or developed the spec.
- `nightly`: same as the [`nightly`](README.md#nightly) property in
`index.json`. The property must only be set when:
  - The URL of the nightly spec returned by external sources would be wrong
    **and** when it cannot be fixed at the source.
  - The code cannot compute the right [`sourcePath`](README.md#nightlysourcepath)
    because the source file of the nightly spec does not follow a common pattern.
  - One or more alternate URLs, used in external sources, need to be recorded in
    an [`alternateUrls`](README.md#nightlyalternateurls) property (note the
    `w3c.github.io` URL of CSS drafts is automatically added as an alternate
    URL, no need to specify it in `specs.json`)
- `tests`: same as the [`tests`](README.md#tests) property in `index.json`. The
property must only be set when:
  - The test suite of the specification is not in a well-known repository.
  - The code cannot determine the correct list of [`testPaths`](README.md#teststestpaths)
    and/or [`excludePaths`](README.md#testsexcludepaths).
- `shortTitle`: same as the [`shortTitle`](README.md#shorttitle) property in
`index.json`. The property must only be set when the short title computed
automatically is not the expected one.
- `forceCurrent`: a boolean flag to tell the code that the spec should be seen
as the current spec in the series. The property must only be set when value is
`true`.
- `multipage`: a boolean flag to identify the spec as a multipage spec. This
instructs the code to extract the list of pages from the index page and fill
out the `release.pages` and `nightly.pages` properties in the list.
- `categories`: an array that is treated as incremental update to adjust the
list of [`categories`](README.md#categories) that the spec belongs to. Values
may be one of `"reset"` to start from an empty list, `"+browser"` to add
`"browser"` to the list, and `"-browser"` to remove `"browser"` from the list.

You should **only** set these properties when they are required to generate the
right info. For instance, some of these properties are needed for Media Queries
Level 3, because the spec uses an old shortname format, leading to the following
definition in `specs.json`, to specify the version of the spec and link it to
other specs in the same series:

```json
{
  "url": "https://www.w3.org/TR/css3-mediaqueries/",
  "seriesVersion": "3",
  "series": {
    "shortname": "mediaqueries"
  }
}
```

The [linter](#lint-before-push) will enforce typical constraints on the
properties, such as making sure that there is only one spec flagged as current
in a series. It will also complain when a property is set whereas it does not
seem needed.


### Compact form preferred

Some of the above properties can be specified with a keyword next to the URL of
the spec, allowing to keep using a string instead of an object in most cases:
- A delta spec can be defined by appending a `delta` keyword to the URL, instead
of through the `seriesComposition`.
- The `forceCurrent` flag can be set by appending a `current` keyword to the URL
- The `multipage` flag can be set by appending a `multipage` keyword to the URL

For instance, to flag the CSS Fragmentation Module Level 3 as the current spec
in the series, the CSS Grid Layout Module Level 2 as a delta spec, and the SVG2
spec as a multipage spec, use the following compact definitions:

```json
[
  "https://www.w3.org/TR/css-break-3/ current",
  "https://www.w3.org/TR/css-grid-2/ delta",
  "https://www.w3.org/TR/SVG2/ multipage"
]
```

This compact form is preferred to keep the list (somewhat) human-readable. The
[linter](#lint-before-push) automatically convert objects to the more compact
string format whenever possible.


### No `index.json` in the pull request

The `index.json` file will be automatically generated once your pull request has
been merged. Please do not include it in your pull request. You may still wish
to re-generate the file (see the [Check before push](#check-before-push) section
below) to check that the generated info will be correct, but please don't commit
these changes.


### Lint before push

Before you push your changes and submit a pull request, please run the linter
to identify potential linting issues:

```bash
npm run lint
```

If the linter reports errors that can be fixed (e.g. wrong spec order, or more
compact form needed), run the following command to overwrite your local
`specs.json` file with the linted version.

```bash
npm run lint-fix
```

**Note:** The linter cannot fix broken JSON and/or incorrect properties. Please
fix these errors manually and run the linter again.


### Check before push

Before you push your changes and submit a pull request, you may also want to
check that the changes will produce the right info. You may
[re-generate the file](README.md#how-to-generate-indexjson-manually) but
generation typically takes several minutes. To only generate the entries that
match the specs that you changed in `specs.json`, you may use the
[diff tool](README.md#build-a-diff-of-indexjson).