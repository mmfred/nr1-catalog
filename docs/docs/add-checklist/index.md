# PR checklist to add a new application to the New Relic One Catalog

When you initiate a pull request to the `nr1-catalog` repository, the following items appear in the pull request template.

## [Nerdpack approver](nerdpack-approver)

The person who signs off on the change ahead of deployment.
- Provide the **GitHub handle** for the final approver of this Nerdpack in your pull request. 
- If known, provide the date when you want the Nerdpack deployed. *Note: the date provides a useful target and we will try to meet it. However, neither New Relic nor the **nr1-catalog maintainer team** are under any obligation to publish on the requested date.*

### [Functional changes](#functional-changes)

List the functional changes in the pull request, highlighting the major features of the application.

- Major feature #1
- Major feature #2

*Note: The rest of this template is a checklist and guide for ensuring that your pull request is swiftly approved.*

## [Project naming](#project-naming-guidance)

- [ ] Repo names and titles do not include the word `New Relic` or `newrelic`
- [ ] Repo names and titles are named with human-readable, functionally descriptive names
- [ ] Repo names are unique within the `nr1-catalog` repository to support the internal project mapping of submodules to uuid’s
- [ ] Project titles use title case (e.g. “Deployment Analyzer”, not “Deployment analyzer”)
- [ ] Projects do not include profanity or crude language in titles, repo names, or any documentation

## [Basic Nerdpack validation](#basic-nerdpack-validation)

Validate that the following are present in your PR ahead of submitting:

- [ ] Your application has been added as a [git submodule](https://git-scm.com/book/en/v2/Git-Tools-Submodules) to the nr1-catalog repo under the `apps` directory (i.e. `nr1-catalog/apps/YOUR_REPO` with an entry in the `.gitmodules` file)
- [ ] No other code in the `nr1-catalog` repo has been modified by your PR
- [ ] The project contains only one Nerdpack with as many artifacts (launchers, nerdlets, etc.) as you choose

### [Features and roadmap](#features-and-roadmap)

- [ ] Does this application have a workflow and/or documentation within the UI for managing an empty state?

## [Repository setup](#repository-setup)

Your linked submodule repository must contain:

- [ ] an `nr1.json` file in the root of your project [example here](https://github.com/newrelic/nr1-catalog/tree/master/examples/nr1.json) containing the following fields:
  - [ ] `schemaType` with a value of `NERDPACK`
  - [ ] `id` with a uuid set by the nr1 CLI (i.e. `nr1 nerdpack:uuid -gf`)
  - [ ] `displayName` with the name of the Nerdpack as it will be displayed in the New Relic One Catalog (e.g. `Browser Analyzer`)
  - [ ] `description` that has the short description of the application's functionality
- [ ] `icon.png` in the root of your project with the dimensions of **48x48**
- [ ] a `package.json` ([example here](https://github.com/newrelic/nr1-catalog/tree/master/examples/package.json)) that contains:
  - [ ] a `version` that follows [semantic versioning standards](website.link)
  - [ ] a `scripts.eslint-check` command
  - [ ] a `scripts.eslint-fix` command
- [ ] a valid `LICENSE` file containing an approved permissive license (ex. Apache 2, MIT, BSD) ([example here](https://github.com/newrelic/nr1-catalog/tree/master/examples/LICENSE))

*Note: we do not permit projects that contain **any** [viral licensing](https://en.wikipedia.org/wiki/Viral_license) into the New Relic One Catalog.*

### [Catalog metadata](#catalog-metadata)

Your project must contain a `catalog` directory with the following:

- [ ] a `config.json` [see example](https://github.com/newrelic/nr1-catalog/tree/master/examples/catalog/config.json) containing the following fields:
  - [ ] `tagline` - a brief headline for the application, **30** characters or less
  - [ ] `repository` - URL to the git repository, less than **1000** characters long
  - [ ] `details` - description of the usage and utility of the application, less than **1000** characters using carriage returns for formatting and no markdown or HTML markup
  - [ ] `support` object containing:
    - [ ] `issues` - a **valid URL** to the repositories issues list (generally the GitHub issues tab for the repo)
    - [ ] `email` - a **valid email address** of the team supporting the application (for New Relic, that generally takes the form of `opensource+<repo name>@newrelic.com`)
    - [ ] `community` - a URL to a support thread, forum, or website for troubleshooting and usage support
  - [ ] `whatsNew` - a bulleted list of customer-facing changes in this version, less than **500** characters using carriage returns for formatting and no markdown or HTML markup
- [ ] `screenshots` - a directory containing at most **6** image files that each comply with the following guidance:
  - 3:2 aspect ratio
  - .png format
  - landscape orientation
  - at least 1600px wide
- [ ] `documentation.md` - a markdown file containing no HTML markup nor any markdown images ([example here](https://github.com/newrelic/nr1-catalog/tree/master/examples/catalog/documentation.md))
- [ ] (optional) `additionalInfo.md` - a markdown file containing no HTML markup nor any markdown images ([example here](https://github.com/newrelic/nr1-catalog/tree/master/examples/catalog/additionalInfo.md))

## [Code guidance](#code-guidance)

*The following are meant to highlight the types of common issues that can degrade performance for your application.*

The code does not:

- [ ] violate the pinned versions of libraries for `react` and `react-dom` of **16.6.3** and `d3` of **3.5.17**
- [ ] contain hard-coded New Relic account IDs, user IDs, or other identifiers that should be retrieved via the NR1 API based on the user viewing the nerdpack
- [ ] use `await` with React's `setState` method
- [ ] perform imperative data fetching (e.g. `NerdGraphQuery.query`) in the `render` method

The code does:
- [ ] make efficient use of the `PlatformStateContext` and `NerdletStateContext` (i.e. not wrapping the entire Nerdlet in a state context when it's not necessary)
- [ ] handle the asynchronous nature of `setState` updates
- [ ] whenever appropriate, extend `React.PureComponent` versus `React.Component` with its React components 
- [ ] implement the `shouldComponentUpdate` React lifecycle method when its React components extend `React.Component` 

### [Design guidance](#design-guidance)

- [ ] The code does not override or amend core New Relic One styles

### [Security guidance](#security-guidance)

The code does not:

- [ ] contain hard-coded API keys, access tokens, or other security credentials
- [ ] include additional `SCRIPT` tags
- [ ] write data to an outside source
- [ ] interact with an outside URL without clear documentation on what is being retrieved
- [ ] write unspecified object data to `NerdStorage`

The code avoids using:

- [ ] the `eval` command
- [ ] `Function` as a constructor (i.e. `new Function(...)`)
- [ ] `dangerouslySetInnerHTML` in React

### [NerdStorage guidance](#nerdstorage-guidance)

Where appropriate, the code follows the guidance regarding [NerdStorage limits and usage](https://developer.newrelic.com/build-tools/new-relic-one-applications/nerdstorage)

The code **does not**:

- [ ] store New Relic credentials in `NerdStorage` (GraphQL should be used to access New Relic data)
- [ ] store third-party SaaS credentials in `NerdStorage` **UNLESS** a warning about the lack of encryption at rest and possibility of access by New Relic employees is prominently displayed, such as in the GitHub README ([example here](https://github.com/newrelic/nr1-github/blob/master/README.md#using-github-personal-access-tokens)) and/or in the documentation portion of the New Relic One Catalog documentation ([example here](https://github.com/newrelic/nr1-github/blob/master/catalog/documentation.md#using-github-personal-access-tokens))
- [ ] store personal data ([PII](https://www.gdpreu.org/the-regulation/key-concepts/personal-data/)) in `NerdStorage`


## [New Relic only](#new-relic-only)

*Note: These concluding sections of instruction are for projects submitted by New Relic teams. If that doesn't apply to you, ignore this section in your PR.*

- [ ] a standard `CONTRIBUTING.md` file in the root of the project ([example here](https://github.com/newrelic/nr1-catalog/tree/master/examples/CONTRIBUTING.md))
- [ ] a standard `CODE_OF_CONDUCT.md` file in the root of the project ([example here](https://github.com/newrelic/nr1-catalog/tree/master/examples/CODE_OF_CONDUCT.md))
- [ ] a standard `cla.md` file in the root of the project ([example here](https://github.com/newrelic/nr1-catalog/tree/master/examples/cla.md))
- [ ] a `README.md` that contains:
  - [ ] the [`New Relic One Catalog`](https://github.com/newrelic/open-source-office/blob/master/examples/categories/index.md#nr1-catalog) project header at the top of the file
  - [ ] Description of what the project does
  - [ ] cloning and setup explanation for the code (including installing dependencies) for local development
  - [ ] How to run the Nerdpack locally
- [ ] your project makes use of New Relic's open source [eslint configuration](https://github.com/newrelic/eslint-plugin-newrelic) by including the following:
  - [ ] the appropriate development dependencies by running `npm install --save-dev @newrelic/eslint-plugin-newrelic eslint prettier` ([see example](https://github.com/newrelic/nr1-catalog/tree/master/examples/package.json))
  - [ ] the `.eslintrc.js` configuration in the root of your project [example here](https://github.com/newrelic/nr1-catalog/tree/master/examples/.eslintrc.js)
  - [ ] the `.prettierrc.js` configuration in the root of your project [example here](https://github.com/newrelic/nr1-catalog/tree/master/examples/.prettierrc.js)

### Additional code guidance for New Relic

*Note: this is for New Relic projects only.*

- [ ] JavaScript in your nerdpack must use ES6
- [ ] The code should make use of the New Relic One components. Whenever possible, the code should avoid using an external OSS component in favor of highlighting the New Relic One SDK's capabilities
- [ ] The code should make use of the New Relic One `Stack` and `Grid` components for layout, as these components respect spacing automatically (*Note that CSS flexbox and grid are more complex than they seem at first, and sometimes they’re a bit tricky to get exact results.*)