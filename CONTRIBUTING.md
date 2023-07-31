# Contributing to this repository

Only `Automaducks` Team members can contribute to the repository!

## Background

The team website code resides in this repository and is hosted through [Github Pages](https://pages.github.com/) as a [Jerkyll](https://jekyllrb.com/) plugin.
You can look at [Github Pages with Jerkyll](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll) for more information.

This mean that after the initial setup, the team can easily add or modify content using [Markdown](https://www.markdownguide.org/cheat-sheet/) markup language, which is much simpler that HTML (this page is written in Markdown).

Try using markdown online: [Stack Edit](https://stackedit.io/app#)

## Getting Started

Clone the repository locally and open it in VSCode:

```PS
# Clone the Repository
cd
cd .\Repos
git clone https://github.com/FTC-23206/.github.git automaducks-home

# Open in VSCode
cd .\automaducks-home
code .
```

## Key Folders

```PS
|
| docs                      # WebSite code
|  | _data
|  |   | navigation.yml     # Navigation Menu
|
|  | pages                  # WebSite Pages (Change navigation when adding new pages)
|  |   | Odometry.md        # Sample page started for the team odometry
|  | _posts
|  |   | design             # New files added here automatically show up as blog entries
|  |   |   | 2023-07-10-title.md

```

## Adding a blog entry

1. Make a copy of `2023-01-01-template.md`
2. Change the date and title on the filename
3. Update the header (*front matter*) with the entry data
4. Add your content to the page (Use VSCode markdown preview to preview your content)
5. Submit a pull-request

See <https://jekyllrb.com/docs/posts/> for more information.

## Adding a page

1. Make a copy of odometry.md
2. Rename the file
3. Add you content to the page (Use VSCode markdown preview to preview your content)
4. Update the navigation.yml
5. Submit a pull request

## Advanced

It is much more convenient to render the website locally prior submitting a pull request when making large changes. This requires *Jerkyll* to be installed locally. See <https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/creating-a-github-pages-site-with-jekyll> for instructions.