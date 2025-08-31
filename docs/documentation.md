
# Documentation

## Markdown

- Pound signs are for titles, the more “#”, the deeper the level
- Create unordered (bullet point) lists with “-”, “*”, or “+”.
- Sub lists must be indented four spaces more than the parent.
- Add links with `“[text](url)”`
- Add images with `“![text](filename)”`
- Can link within the document, just need the URL to use “#” and the sub-section.
- Bold text by wrapping it in double asterisks or double underscores.
- Italicize text by wrapping it in single asterisks or single underscores.
- Prefix lines with “> “ to make block quotes.
- Create ordered lists by just using numbers followed by a period and space.
- Use back ticks to wrap code.
- Add horizontal rule with three or more hyphens, asterisks, or underscores.
- Tables use pipes to delimit columns, and at start/end.  Header is separated from body using three or more hyphens in cells.

Example table syntax:
```
| Syntax      | Description |
| ----------- | ----------- |
| Header      | Title       |
```

## Mermaid

### Flowcharts

- Start by declaring it with “flowchart” followed by a direction, “LR” for left to right, or “TD” or “TB” for top down or top to bottom, 
  which are the same thing.  There is also RL=right to left, BT=bottom to top.  Subsequent lines are nodes and/or edges in the graph, 
  and must be indented.
- Simple text indicates a node, you can optionally set its text inside square brackets, quotes are optional but enable unicode.  
  Use both quotes and back-ticks to enable markdown formatting.
- Favorite Node Shapes:
    - “[text]” - rectangle, pointed edges.  “rect”
    - “(text)” - rounded edges.  “rounded”
    - “([text])” - stadium, circular sides.  “stadium”
    - “[[text]]” - has side-boxes, subroutine style,  “subproc” type.
    - “[(text)]” - cylinder, like a database.  “cyl”
    - “((text))” - a circle.  “circle”
    - “{text}” - a rhombus, like a decision point.  “diamond”
    - “{{text}}” - a hexagon.  “hex”
    - “@{ shape: xxx, label: text }” - lets you pick a specific shape.
    - Horizontal cylinder: “das” type.
- Links:
    - “-->” arrow, “--x” crossed out link, “--o” circle ended link.
    - “---” connection
    - “==>” thick link
    - “-.->” dotted link
    - To add text, first write the first two characters of the link, then a space, your text, another space, then the full pattern. 
      “-->” becomes “-- text →” for example.  Alternatively, write the link, then your text enclosed in pipes.
- You can chain links.
- You can link to multiple nodes by separating them with “&”
- You can declare nodes and links in the same line.
- You can make links longer by inserting more hyphens, dots, or equal signs.  This can enable you to put nodes farther down or 
  to the side in the chart.
- Subgraphs: use the keyword “subgraph” followed by its name.  Use “end” to stop defining the sub-graph.  
  It will be inside its own box.  You can declare edges between sub-graphs too, as well as nodes inside them.  
  You can set sub-graph direction with the “direction” keyword, followed by a direction declaration.

### Sequence Diagrams

- Declared with “sequenceDiagram”, remaining lines are indented.
- Basic format: participant, connection, other participant, colon, text for connection.
- Participants are shown from left to right in order of appearance.
- Connections:
    - Lines are solid if there is one hyphen, dotted if there are two.
    - Endings: “>” nothing, “>>” closed arrow, “x” crossed out, “)” open arrow..
    - Open arrows imply something is asynchronous.

### State Diagrams

- Declared with “stateDiagram”, remaining lines are indented.
- Basic format: state, connection, state.  To add text, append a colon then the text.
- Start and end states are represented with “[*]”, if it’s on the left then it’s the start, right, the end.
- Composite states are nested diagrams, in their own box, defining internal states.
- Define a composite state with: state name { … }

## Github Pages

- available for free for public repos.  Private repos require github pro.
- requires a sub-path that equals the repository name.
    - URLs will be like: `https://<user|organization>.github.io/<repository>`
- You can have a website with no sub-path, it requires naming the git repository
  `<user>.github.io`.  Then its URL will be like: `https://<user|organization>.github.io/`
- Even free accounts can have one github pages site per public repository.
- github actions can create the site, it is free for public repositories that don't exceed a 
  usage limit.
- Github pages has native support for Jekyll, you don't need to write a workflow to have a Jekyll site hosted.
  However, it has template workflows for several static site generators.
- You set a publishing source, which is a branch and a folder within it.  The folder can be the root, or "docs/".
  No other folders are allowed unless you use github actions.
- You do not need to generate and commit the static site files if you use a github workflow.
  You can avoid using a github workflow if you generate and commit the static site files.
    - I recommend using the custom workflow, it's easy to do, and it means your repo will be lighter.
- Github has some support for using a custom domain for your site.
- The docs directory can include markdown, html, css, javascript, images, and other static web content files.
- If you use a [github action to produce the pages site](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site), you can specify the directory holding the static
  site, so you are not forced to use any particular directory.
- See [creating github pages](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site).
- If a static site defines a `404.html` page, it will be used when a page is not found.

## BitBucket Pages

- I think it's not free to use Bitbucket at all.
- Owned by Atlassian
- Pages are available for public and private repos too.
- The site can be hosted at the root of the domain, which is not possible with Github.
- It adds some javascript automatically for tracking purposes.
- It sets cache headers automatically, lasting 15 minutes.

## Static Site Generators

### Hugo

- use the "content" directory for markdown files.
- configuration:
    - my preference: use hugo.yaml
    - set baseURL, title, languageCode=en-us, params (a miscellaneous json object)
    - It supports a "config" directory, literally named config.  Its sub-directories are considered environments,
      so you can specify an environment on the command line and it will load in configuration from that sub-directory.
      Hugo recursively searches these sub-directories, and merges any files that are part of the environment.
      If there is a "_default" directory, that is the base configuration, and other environments may override its settings.
      The default environment is "development" if you run hugo server, or "production" when simply running hugo, but 
      the sub-directories do not need to exist.
    - themes can also set configuration.  A theme is a sub-directory of the "themes" directory.  Your root
      configuration file can select more than one theme to make active, hugo will merge settings from them in the order
      they appear in your "hugo.yaml" file's themes list.
- archetypes: These are templates for the creation of new content.  Content is any file under the content directory, and
  these are usually markdown files.  So the archetype is usually just pre-filling some of the front-matter. 
  archetypes are used in the "hugo new content" command.  It chooses what archetype to 
  use based on the "--kind" argument, then the path of the content.  It can consider default archetypes, and archetypes
  defined in themes.  I personally prefer to just not use archetypes, nor the "hugo new content" method, and to instead
  simply write the front matter myself.  It's much simpler.
- Files named "_index.md" add content to the URL represented by the directory they are in.  Other files add content to the 
  URL for that file's path.  The file "content/foo/_index.md" will modify the URL "/foo", while the file "content/foo/index.md" 
  will modify the URL "/foo/index"
- Sub-directories under content are considered sections of the site.
- front matter: metadata at the top of a markdown file.
    - often used: date, title, weight, draft.

### Mkdocs

- More intuitive in nature.  Based on Python and jinja2 templates.
- configured with a mkdocs.yml file
- by default, markdown content is stored in the "docs" folder.  This is also the folder that
  github pages expects to use for HTML, so you will need to override that if you expect to use
  mkdocs to create a github pages site.  Alternatively, use a github action to generate the
  static site, and you don't need to change any of the conventions. 
    - Set "docs_dir" to "content" to mimic hugo.
    - Set "site_dir" to "docs" so github pages can find it.
- Supports mermaid diagrams.

## Jekyll

- More broadly used and supported
- Github pages supports this without needing a custom workflow
- Based on Ruby
