# Meta

This section contains info about this book *itself*.

## Developing

This book is written via [`mdBook`][mdbook] and deployed via GitHub Actions to
GitHub pages.

See [Using deploy via actions][deploy-actions] [`mdBook`][mdbook] Wiki page for
the CI part.

Linkcheck not included, I find that more annoying versus the value it provides.

[mdbook]: https://rust-lang.github.io/mdBook/index.html
[deploy-actions]: https://github.com/rust-lang/mdBook/wiki/Automated-Deployment%3A-GitHub-Actions#using-deploy-via-actions

## Style guide

- No column width limit. Do not hard-wrap lines. Line wrapping makes diffs impossible.
- Stick to `#` for headings.
- Leave newline between titles (e.g. `# XXX` or `## XXX`) and their content.
- Ensure links are `<>`-surrounded, or otherwise use footnote-style links.
