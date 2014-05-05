# Supplejack docs

These docs are built using Jekyll.

## Using [Jekyll](http://jekyllrb.com/docs/home/)

To get started editing the documentation run the following commands

```bash
~ $ gem install jekyll
~ $ cd path/to/supplejack
~ $ git checkout gh-pages origin/gh-pages
~/supplejack $ jekyll serve
```

This will run a local coy of the docs at http://0.0.0.0:4000/supplejack/

## Editing pages

Existing pages are under the '_posts' directory. All these files use [Markdown](http://daringfireball.net/projects/markdown/).

Once you have made and saved your changes run the following command to generate the HTML for the page:

```bash
# From the root of the supplejack repository
jekyll build
```

If you are doing lots of changes or eould like to review your changes often you can use 

```bash
jekyll build --watch
```

This will rebuild the site automatically when you save changes. Once you are happy with your changes commit them and push to Github. As soon as you have pushed you should see the changes reflected on http://digitalnz.github.io/supplejack/

**Note:** For every file that you have edited there should be a corresponding HTML file to commit too.

## Creating new pages

To create a new page run the following commad in the root of the docs site.

```bash
ruby ./bin/jekyll-page "Page name" page-category 
```

This script builds a new page with the correct time stamping and create a symlink in the pages directory.

### Template
Supplejack docs are built using [jekyll-docs-template](http://bruth.github.io/jekyll-docs-template)
http://bruth.github.io/jekyll-docs-template
