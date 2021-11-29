# [Supplejack docs](http://digitalnz.github.io/supplejack/)

These docs are built using Jekyll

## Using [Jekyll](http://jekyllrb.com/docs/home/)

To get started editing the documentation run the following commands

```bash
~ $ git clone git@github.com:/DigitalNZ/supplejack supplejack_docs
~ $ cd supplejack_docs
~ $ rbenv install $(cat .ruby-version)
~ $ gem install bundler
~ $ bundle exec jekyll serve -p 3005
```

This will run a local copy of the docs at http://0.0.0.0:3005/supplejack/

## Editing pages

Existing pages are under the '_posts' directory. All these files use [Markdown](http://daringfireball.net/projects/markdown/).

Once you have made and saved your changes, refresh your browser, the changes should be there.

If you are doing lots of changes or would like to review your changes, just run

```bash
git diff
```

## Creating new pages

To create a new page run the following commad in the root of the docs site.

```bash
ruby ./bin/jekyll-page "Page name" page-category
```

This script builds a new page with the correct time stamping and create a symlink in the pages directory.

### Template

Supplejack docs are built using [jekyll-docs-template](http://bruth.github.io/jekyll-docs-template)
http://bruth.github.io/jekyll-docs-template
