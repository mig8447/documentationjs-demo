documentationjs-demo
====

A minimal demo on the basics of [Documentation.js][1]. See a demo on the
generated documentation at https://github.com/mig8447/documentationjs-demo

# Installation

1. Clone or download this repository
2. Open a terminal client
3. Change directories into this repository's directory
4. Execute:
    ```sh
    npm i
    npm run build
    ```
    This will generate HTML documentation in `dist/doc`,

# Alternative Usage

This will monitor the target files and recreate the documentation on every
change

Or start "doc development" using

```sh
npm run watch
```

Which will start the doc "watching" and a server that will auto-reload whenever
the documentation changes, as well as opening your browser in that page

# Things to note

- Doc-related unitary tasks `doc*` are defined in the `package.json` and are not
  intended for standalone use but as things the bigger tasks can use
- The `doc` script leverages the `documentation` command line, for which you can
  find more information at [their page][1].
- The `doc:watch` script leverages option passing to another `npm` script by
  using the standard options shell terminator `--`. Anything that comes after it
  is passed directly as arguments to the `npm` task. That way we don't have to
  re-define the task just to add or change an argument
- The `doc:serve` task uses `reload` to start a server in a port defined by
  either the `PORT` environment variable or the `config.port` property in the
  `package.json` file (If no `PORT` is set)
- The `watch` task also uses the `concurrently` package to start both the
  documentation generation process and the auto-reloading server at the same
  time
- We make use of the [`npm`'s pre and post scripts][2]

See a commented-out version of the `package.json`'s scripts section below:

```js
{
    // ...
    // Default values exposed as the `npm_package_config_...` environment variables
    "config": {
        // Default port is 8080
        "port": 8080
    },
    // ...
    "scripts": {
        // SECTION Unitary scripts not intended for standalone use
        // Clean the doc directory to start fresh
        "doc:clean": "rm -rf ./dist/doc",
        // Build the actual documentation by monitoring any javascript files in
        // the `src` folder hierarchy. Output in HTML format and use the
        // `documentation.yml` file to build the table of contents
        "doc": "documentation build 'src/**/*.js' -f html -o dist/doc --config documentation.yml",
        // Start the documentation.js generation in watch mode
        // NOTE: This does not monitor the `documentation.yml` config file
        "doc:watch": "npm run doc -- -w",
        // Serve the `dist/doc` documentation files in the port corresponding to
        // the `PORT` environment variable if available or the port in the
        // config object above if `PORT` was empty. Open the doc in the browser
        // automatically. The page will be reloaded whenever any of the files in
        // `dist/doc` change
        "doc:serve": "reload -p \"${PORT:-$npm_package_config_port}\" -d dist/doc -b",
        // !SECTION Unitary scripts not intended for standalone use

        // SECTION Scripts intended standalone use
        // Clean the built files to start fresh
        "clean": "npm run doc:clean",
        // Execute the clean task before the build task is executed
        "prebuild": "npm run clean",
        // Build the actual doc
        "build": "npm run doc",
        // Build the doc before watching so that there's something available for
        // the doc:serve task
        "prewatch": "npm run build",
        // Generate documentation in watch mode and serve the generated
        // documentation with an auto-reloading page
        "watch": "concurrently \"npm run doc:watch\" \"npm run doc:serve\"",
        // Build the project before deploying to github pages
        "predeploy": "npm run build",
        // Deploy only the docs as the github page
        "deploy": "gh-pages -d ./dist/doc"
        // !SECTION Scripts intended standalone use
    },
    // ...
}
```

[1]: http://documentation.js.org/ (Documentation.js)
[2]: https://docs.npmjs.com/cli/v6/using-npm/scripts#pre--post-scripts (NPM's pre and post scripts)
