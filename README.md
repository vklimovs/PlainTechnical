# PlainTechnical

PlainTechnical is a Vale style for technical English. It asks for the short
sentences of ASD-STE100 Simplified Technical English and reports the phrases
that machine-written prose repeats. It has no tie to ASD, and it does not
check compliance with the standard.

```ini
StylesPath = .vale/styles
Packages = https://github.com/vklimovs/PlainTechnical/releases/download/v1.0.0/PlainTechnical.zip

[**/*.{yaml,yml}]
View = Comments
```

Run `vale sync`, then `vale <files>`. The package maps the YAML of an Ansible
role alone, and a project maps the rest, as above. A project with roles names
those paths, as docs/design.md shows. For a commit, a hook runs
`vale --minAlertLevel=warning --output=line --path=COMMIT_EDITMSG < "$1"` and
fails on any output. The tests run on Vale 3.26.0, from the root of the repo:

```sh
vale --no-global test --coverage styles tests/*.test.yml
vale --no-global --config=tests/views/.vale.ini test tests/views
vale --no-global --config=tests/errors/.vale.ini test tests/errors
vale --no-global --config=tests/commits/.vale.ini test tests/commits
```

A release zip is the output of
`git archive --format=zip --prefix=PlainTechnical/ <tag> .vale.ini styles LICENSE`.

The file docs/design.md lists the sources and the choices between them.
