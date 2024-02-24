## CONTRIBUTING.md

In addition to the CODE_OF_CONDUCT in this repo:

## Contents

- [Issues](#Issues)
   - [Type of Issues](#Type+of+Issues)
   - [Severity of Issues](#Severity+of+Issues)
- [Security Features](#Security+Features)
- [Editor Settings](#Editor+Settings)
- [Coding Style](#Coding+Style)

- [Commits and Pull Requests](#CCommits+and+Pull Requests)
- [Testing](#Testing)
- [Code Reviews](#Code+Reviews)

- [Submitting Pull Requests](#Submitting+Pull+Requests)
- [Certificate of Origin](#Certificate+of+Origin)
- [Attribution](#Attribution)

<hr />

## Issues

Security vulnerabilities should not be mentioned publicly as issues.<br />
Instead, email us directly.

GitHub Issues are not the appropriate place to ask questions or debug specific projects, but should be reserved for filing bugs and feature requests.

Before creating a new issue, please ensure the issue was not already reported by reviewing Open and Close Issues.


### Type of Issue

Each issue is flagged with one or more of these types:

   * <strong>Discussion</strong> for issues that can be non-issues, and encompass best practices, or plans for the future.

   * <strong>Easy First Step</strong> for issues that are great for new contributors to get started with. We want people to feel like there's always somewhere you can start.

   * <strong>Quick</strong> for small issues (such as typos in text) that can be fixed quickly.

   * <strong>To check</strong> for issues that may not be reproducible, or have not been vetted by a team member.

   * <strong>Defect</strong> for bugs in functionality. The issue text should contain steps to reproduce. Feel free to fix these and submit a pull request.

   * <strong>Enhancement</strong> for enhancements to functionality. If you would like to work on one, please add a comment that you are doing so.

   * <strong>Workaround known</strong> for issues have had their solutions discussed, but have yet to be implemented.
  <br /><br />

We prefer that "+1" or emoji be added to an existing issue instead of generic comments. [reactions](https://github.blog/2016-03-10-add-reactions-to-pull-requests-issues-and-comments/).


<hr />

### Severity of issues

The estimated impact of each issue is categorized using these labels (from least to most severe):

* <strong>Trivial</strong>: Minimal impact on the usefulness and functionality of the software; a "nice-to-have." Visual impact without functional impact;

* <strong>Medium</strong>: Some impairment of use, but simple workarounds exist;

* <strong>Critical: Significant loss of functionality or impairment of use. Display of telemetry data is not affected though. Complex workarounds exist;

* <strong>Blocker</strong>: Major functionality is impaired or lost, threatening mission success. Display of telemetry data is impaired or blocked by the bug, which could lead to loss of situational awareness.


<hr />

## Security Features

Contributors to this GitHub repository should be aware of the following security features:

* To protect this repository against unauthorized changes, the <a target="_blank" href="https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches">"Require review from Code Owners"</a> is activated so that GitHub automatically requests a review from Code Owners when a Pull Request (PR) is opened. The <a target="_blank" href="https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/about-code-owners#example-of-a-codeowners-file">CODEOWNERS</a> file (at the root or /docs folder) specifies the folder, file, or file type to be reviewed by the GitHub account or email address of individual or team specified. 

* Reviewers of that CODEOWNERS file (and its location) are specified in another CODEOWNERS file in the repo's /.github folder.

* To control what actions are allowed in this repository, this repo makes use of one or more <a target="_blank" href="https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets">rulesets</a> listing <a target="_blank" href="https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/creating-rulesets-for-a-repository#using-fnmatch-syntax">rules</a>. A ruleset for a feature branch can require signed commits, block force pushes, define who can push commits to a certain branch, or who can delete or rename a tag.

* To withstand man-in-the-middle attacks attempting to steal data during transmission, our contributors need to use secure HTTPS protocol by setting up SSH (Secure Shell Protocol). SSH keys are a way to identify trusted computers, without involving passwords. It's enabled by clicking your avatar, "Your profile", settings, "SSH and GPG keys" at <a target="_blank" href="hhttps://github.com/settings/keys">https://github.com/settings/keys</a>.[<a target="_blank" href="https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account">DOC</a>]

  TODO: Use <a target="_blank" href="https://tutorials.akeyless.io/docs/using-ssh-certificates-to-access-remote-machines">AKeyless to dynamically create SSH keys</a> rather than leaving manually-created static SSH keys which can be stolen from your computer.

* To ensure that the email addresses specified in git commits are <strong>verified</strong>, our contributors need to associate a GPG key to their account. It's enabled by clicking your avatar, "Your profile", settings, "SSH and GPG keys" at <a target="_blank" href="https://github.com/settings/keys">https://github.com/settings/keys</a>. See <a target="_blank" href="https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key">this</a> for more information.

* To reduce the ability for malicious actors to make commits as you, our contributors need to have 2FA (Two Factor Authentication). It's enabled by clicking your avatar, Your profile, settings, "Password and authentication" at <a target="_blank" href="https://github.com/settings/security">https://github.com/settings/security</a>. See <a targeet="_blank" href="https://docs.github.com/en/authentication/securing-your-account-with-two-factor-authentication-2fa/configuring-two-factor-authentication">this</a> for more information.

* To further block malicious actors from executing potentially destructive commands, the usual IP address used by  Administrators of this repository is registered in this GitHub Enterprise Cloud repository's <a target="_blank" href="https://docs.github.com/en/github/setting-up-and-managing-organizations-and-teams/managing-allowed-ip-addresses-for-your-organization">IP Allow List</a>. A side affect of this is that GitHub Codespaces cannot be used for repositories protected this way since their IP addresses are dynamically assigned.

* To identify and remediate security vulnerabilities in package dependencies, this repository is updated immediately when <a target="_blank" href="https://help.github.com/en/github/managing-security-vulnerabilities/about-security-alerts-for-vulnerable-dependencies">GitHub Dependabot Security Advisories</a> are issued.


<hr />

## Coding Style

To foster productivity, we ensure consistency throughout the source code by using code formatters and linters specific to each programming language. Not doing so would prolong team reviews that waste time on superficial aspects of code rather than improving its functionality and performance.

We optimize for developer productivity and happiness through teamwork.

This repo contains open source software. Since others in the future will read this, we want to make it look nice and easy for them. It's sort of like driving a car: Perhaps you love doing donuts when you're alone, but with passengers, the goal is to make the ride as comfortable as possible.


<hr />

## Editor Settings

There are a lot of tools that do the same thing, and we try to pick the best one for the job. 
For team-level efficiency, we try to keep the number of tools we use to a minimum.

As for <a target="_blank" href="https://wilsonmar.github.io/text-editors/">text editor IDE</a>, most developers are now using the free VSCode (Microsoft's Visual Studio Code).

You are not required to use Visual Studio Code, but many find it useful, so at the root of this repo is the <tt>.vscode</tt> folder which specifies automatic actions such as run and debug without switching between VSCode and the debuggere for <a target="_blank" href="https://github.com/godotengine/godot-vscode-plugin/issues/163">C# godot</a>, <a target="_blank" href="https://bryanwweber.com/writing/2022-01-24-create-tasks-json-vs-code.html">python</a>, <a target="_blank" href="https://learn.microsoft.com/en-us/training/modules/debug-nodejs/?source=recommendations">JavaScript</a>, <a target="_blank" href="https://github.com/microsoft/vscode-java-debug/blob/main/Configuration.md">java</a>, <a target="_blank" href="https://www.jpatrickfulton.dev/blog/2023-07-03-gatsby-vscode-support/#the-vscode-folder">Gatsby</a>, <a target="_blank" href="https://piethein.medium.com/dockerize-your-projects-in-visual-studio-code-f2374de541bd">Dockerfile</a>, <a target="_blank" href="https://blog.nonstopio.com/the-hidden-gems-of-vs-code-settings-json-and-launch-json-explained-9e9e1c6b4b4a <a target="_blank" href="https://docs.nvidia.com/nsight-visual-studio-code-edition/cuda-debugger/index.html">CUDA</a>, etc. 

The .vscode <a target="_blank" href="https://code.visualstudio.com/docs/getstarted/settings#_workspace-settings">Workspace settings folder</a> specific to the project of the GitHub repo contains these files:

  * **extensions.json** contains project-specific extensions, such as <a target="_blank" href="https://www.youtube.com/watch?v=kyRclsioJBQ">VIDEO</a>: Console Ninja which shows the result of each line within VSCode, which eliminates the need for logging and switching back-and-forth between code and console window. <a target="_blank" href="https://www.youtube.com/watch?v=kyRclsioJBQ&t=4m25s">"Random Everything"</a> to generate test data within VSCode. <a target="_blank" href="https://www.youtube.com/watch?v=kyRclsioJBQ&t=7m19s">"Toggle Quotes"</a> to quickly switch between single and double quotes.

  * **launch.json** contains configuration profiles to execute the project from the Run and Debug menu. <a target="_blank" href="https://go.microsoft.com/fwlink/?linkid=830387">DOCS</a>: For example, to compile the extension and then opens it inside a new window for IntelliSense autocomplete. 
  
  * **tasks.json** - <a target="_blank" href="https://code.visualstudio.com/docs/editor/tasks">DOC</a>: task.json code instructs VSCode to invoke a <strong>type</strong> of processor to invoke the <strong>script</stong> defined. Built-in task types are gulp, grunt, jake, and npm. A tasks is run from Quick Open (⌘P) and typing 'task', Space and the command name, such as <tt>task lint</tt>. To keep settings agnostic to the enviornment, use <a target="_blank" href="https://code.visualstudio.com/docs/editor/variables-reference">reference variables</a> for <a target="_blank" href="https://code.visualstudio.com/docs/editor/tasks#_variable-substitution">substitution</a>, such as <tt>${workspaceFolder}</tt> and <tt>${file}</tt> to specify the location of the script to run. 

  * **settings.json** - overwrites VSCode app <a target="_blank" href="https://code.visualstudio.com/docs/getstarted/settings#_default-settings">default settings</a> and user settings for search, spelling, etc. <a target="_blank" href="https://realpython.com/advanced-visual-studio-code-python/#adding-bonus-extensions-to-visual-studio-code">DOC</a>.
  <a target="_blank" href="https://bobbyhadz.com/blog/what-is-vscode-folder">BLOG</a>: <a target="_blank" href="https://www.youtube.com/watch?v=sIhmrUvFLmA">VIDEO</a>: The contents of settings.json are revealed by pressing <tt>⌘,</tt> (Mac) or <tt>Ctrl+,</tt> (Windows) to open the Settings editor, then click the "Workspace" tab.

  <ul><pre>{
  "editor.formatOnSave": true,
  "editor.wordwrap": "on",
  "editor.tabSize": 2,
  "editor.fontSize": 10,
  "editor.InsertSpaces": true,
  files.eol": "\n",
  "files.trimTrailingWhitespace": true,
  "files.insertFinalNewline": true,
  "files.trimFinalNewlines": true,
  "files.autoSave": "onFocusChange",
  "[css]":{
      "diffEditor.codeLens": true
  },
  "files.exclude": {
      "**/.git": true,
      "**/.svn": true,
      "**/.hg": true,
      "**/CVS": true,
      "**/.DS_Store": true
  }
}
  </pre></ul>

   * A space after each list item and method parameter (`[1, 2, 3]`, not `[1,2,3]`), around operators (`x += 1`, not `x+=1`), and around hash arrows
   * two spaces (soft tabs)
   * No trailing whitespace on each line
   * Blank lines should not contain any space
   * No blank line at end of file
   <br /><br />

In IaC:
   * So that we can consistently serve images from the CDN, always use image_path or image_tag when referring to images. Never prepend "/images/" when using image_path or image_tag.
   * For the CDN, use cwd-relative paths rather than root-relative paths in image URLs in any CSS. So instead of url('/images/blah.gif'), use url('../images/blah.gif').
   <br /><br />

* Set your editor to keep each commit message 72 or less characters. This makes them easier to read in various git tools. Set in your git commit message file: See http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html

   <pre>:set textwidth=72</pre>

At the root of the repo is the <tt>.vscodeignore</tt> file that specifies files and folders to be ignored by VSCode:

    <ul><tt>.gitignore
CHANGELOG.md
   </tt></ul>

<hr />

## Commits and Pull Requests

Please:

* Fork this repository to your account.

* Before making changes, please create a branch off of the main/master branch. Include your GitHub account name (e.g. johndoe-fix-ds).

   <pre><strong>git checkout -b "johndoe-fix-ds"<strong></pre>

* Before submitting a PR, please run tests. Specify in your PR notes what tests have been run.

* Make your commits atomic (one change per commit). Multiple files are added and committed together for the same change.

* Begin commit messages with a keyword that may trigger automatic actions:

   - <tt>fix:</tt> when correcting bugs in the code
   - <tt>feat:</tt> when adding a product features
   - <tt>build:</tt> when adding a new 
   <br /><br />
   - <tt>chore:</tt>    when changing text such as the version number of dependencies
   - <tt>ci:</tt>       when changing a continuous integration component
   - <tt>docs:</tt>     when changing documentation
   - <tt>refactor:</tt> when restructuring code for better readability or execution efficiency
   - <tt>style:</tt>    when changing styling, such as changing an image file
   - <tt>test:</tt>     when changing a component used for testing
   <br /><br />

This repo does NOT use GitHub Workflows to automatically bump the semantic-formatted version code (such as "1.2.3") and create an entry in the CHANGELOG file. 

New release numbers (such as "16.04") are typically scheduled in advance in a Roadmap maintained by product managers because a potentially breaking change requires additional tools to migrate to restructure data.

There 

* Use commit messages to explain **why**, *not what and how* (the code shows that)

In the commit subject line:
* Use square brackets to tag files impacted by change. Examples: 
   - <tt>Fix: typo [README.md]</tt>
   - <tt>Fix: Add avatar [Profile folder]</tt>
   <br /><br />
* Capitalize the first descriptive word (the "Add" in the example above).
* Use the imperative mood in the subject line. Example: "Add", not "Added" or "Adds".
* Make sure the acronyms and abbreviations you use in the team's list of known <a target="_blank" href="https://wilsonmar.github.io/acronyms/">acronyms</a>, abbreviations, and contractions.

* Limit the commit message to 50 characters (this is a GitHub recommendation).
* Do not end the Commit Subject line with a period.
* Separate commit subject from body with a blank line

In commit body lines:
* Include a ticket identifier if you are using an issue tracker such as <a target="_blank" href="https://wilsonmar.github.io/jira/">Jira</a> so that it can be identified for automatic actions:
   - <tt>Resolves #ABC-34</tt>
   <br /><br />
* In the commit body, reference the relevant issue number, if applicable:
   - <tt>Rebased</tt>
   <br /><br />
* Add steps to reproduce, stack traces, compiler errors, library versions, OS versions, and screenshot files (if applicable).
* Use [GitHub-flavored Markdown](https://help.github.com/en/github/writing-on-github/basic-writing-and-formatting-syntax) - put code blocks and console outputs between three backticks (```). This improves readability.
* Are there possible side effects or other unintuitive consequences if this change is made?
* Be precise about the proposed outcome of the feature and how it relates to existing features. Include implementation details if possible.

For more about this, 
   * [Write a great commit message](https://chris.beams.io/posts/git-commit/). 
   * <a target="_blank" href="https://www.youtube.com/watch?v=OJqUWvmf4gg&t=3m">VIDEO</a>: Use yarn to create release.
   * <a target=_blank" href="https://www.youtube.com/watch?v=BbdFfvZNWNw">by Codity</a>
   * <a target=_blank" href="https://www.youtube.com/watch?v=xvPiZyx0cDc">by DevOps Toolkit</a>
   * http://help.github.com/pull-requests/


<hr />

## Testing

All features or bug fixes are tested before each PR is accepted. We test using the "Behavior-Driven Development" (BDD) approach using the [RSpec](http://rspec.info/) framework ???

We have a handful of test  ??? features, but most of our testbed consists of RSpec examples. 
Please write RSpec examples for new code you create.

We use [Travis CI](https://travis-ci.org/) to run our tests. Travis CI is a hosted continuous integration service for the open source community. It is integrated with GitHub and offers first class support for many languages. We use Travis CI to run our tests automatically when we push new code to GitHub.

A full set of tests are run before a release is created. 


<hr />

## Code Reviews

- **Do your best.** No one writes bugs on purpose. Do your best, and learn from your mistakes.

- **Keep tools updated.** Before a code review, all reviewers update all components and libraries to use the latest version as the rest of the team. This is why Python programs have a <tt>requirements.txt</tt> file, Ruby programs have a <tt>Gemfile</tt>, and JavaScript programs have a <tt>package.json</tt> file.

- **Auto format code.** This reduces wasting time on formatting issues. 

- **Review the code, not the author.** Look for and suggest improvements without disparaging or insulting the author. Provide actionable feedback and explain your reasoning.

- **You are not your code.** When your code is critiqued, questioned, or constructively criticized, remember that you are not your code. Do not make code review comments personal.

- Kindly note any violations to the guidelines specified in this document. 

- **Include doc changes with code in PRs.** The docs provide additional information such as attribution for techniques used. This makes it easier and faster for others to understand coding choices made.

- **Keep code working.** Clone to a separate repo or branch to do work, then include changes after verifying that your changes work. This keeps the main repo working for all others. Code reviews by others are done to uncover "blind spots" and unintended consequences from coding and testing.



<hr />

## Certificate of Origin

By making a contribution to this project, I certify that:

- (a) The contribution was created in whole or in part by me and I
      have the right to submit it under the open source license
      indicated in the file; or

- (b) The contribution is based upon previous work that, to the best
      of my knowledge, is covered under an appropriate open source
      license and I have the right under that license to submit that
      work with modifications, whether created in whole or in part
      by me, under the same open source license (unless I am
      permitted to submit under a different license), as indicated
      in the file; or

- (c) The contribution was provided directly to me by some other
      person who certified (a), (b) or (c) and I have not modified
      it.

- (d) I understand and agree that this project and the contribution
      are public and that a record of the contribution (including all
      personal information I submit with it, including my sign-off) is
      maintained indefinitely and may be redistributed consistent with
      this project or the open source license(s) involved.


<hr />

## Attribution

Note: All contributions will be licensed under the project's license.

This document was adapted from excellent examples. 
Much gratitude and respect to the authors of these documents:

Based on commit message, automatically bump Semantic Version and Change Log using GitHub Actions Workflows:
   * https://www.youtube.com/watch?v=4wPjo5C-v8Y by GitKraken
   * https://www.youtube.com/watch?v=mah8PV6ugNY by Dave's Dev Channel
   * https://www.youtube.com/watch?v=cvvOqNorlZQ by Microsoft DevRadio
   * https://www.youtube.com/watch?v=BbdFfvZNWNw by Justin Brooks
   * https://www.youtube.com/watch?v=xvPiZyx0cDc by DevOps Toolkit
   * https://www.youtube.com/watch?v=q3qE2nJRuYM by Julie Ng
   * https://www.youtube.com/watch?v=7pBcuT7j_A0 by Kodaps Academy
   * https://www.youtube.com/watch?v=fcHJZ4pMzBs by Eddie Jaoude