# deepwandering.github.io

Personal academic website deployed with GitHub Pages.

## Line endings

This repository includes `.gitattributes` to keep web source and generated Quarto assets on LF line endings across Windows, macOS, and Linux. This avoids repeated Git warnings such as `LF will be replaced by CRLF` for HTML, CSS, JavaScript, Quarto, Markdown, and YAML files.

If Git has already staged or cached files with inconsistent endings, normalize once with:

```powershell
git add .gitattributes
git add --renormalize .
git status
```
