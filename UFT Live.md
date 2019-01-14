# Drupal 8 Site-Building:
In this section we'll talk about how to use Drupal 8. You can ask questions about differences between D6 and D8, and we'll dive into the UI of your new Drupal site.

## Drupal 8 Views
### Exploring the existing Views
- /admin/structure/views

### Creating a new View
- /admin/structure/views/add

### Adding views to pages
#### Via Blocks
These are standard blocks that are configured via the default blocks interface with all visibility settings available.
- /admin/structure/block
- Place block
- Select Views Block

#### Via Paragraphs
These are blocks added to a specific page via Paragraphs. The visibility is controlled with the view access controls defined on Paragraphs.
- Add a piece of content that allows the "Embed View" paragraph type
- Search for the view
- Select the display
- Optionally:
  - Define the number of items available in results (and associated offset)
  - Define pagination
  - Configure arguments (contextual filters) for this view implementation

#### In a template (Twig Tweak)
- This is the least flexible way to add Views, and should only be used to add blocks that do not require visibility rules, and should be defined on all pages (or whatever level template you place it in). It's sledge hammer where as the other two options are scalpels.

We have the [Twig Tweak](https://www.drupal.org/project/twig_tweak) module installed. This allows us to place blocks, views, menus, etc. directly in templates.
Brows the [General Cheat Sheet](https://www.drupal.org/docs/8/modules/twig-tweak/cheat-sheet-8x-2x), [Views-specific](https://www.drupal.org/docs/8/modules/twig-tweak/twig-tweak-and-views), and [Block-specific](https://www.drupal.org/docs/8/modules/twig-tweak/rendering-blocks-with-twig-tweak) documentation.
- In any template, use the example necessary from the above documentation.

e.g. `{{ drupal_view('who_s_new', 'block_1') }}`

We don't currently use this to display views, but we do use it to display Blocks, and Menus.

e.g. `/templates/layout/region--header.html.twig`

Note: You can also show blocks that are configured via the blocks interface using `{{ elements.whatever }}` as shown in `/templates/layout/region--footer.html.twig` and `/templates/layout/region--off-canvas.html.twig`

## View Modes
We use view modes extensively in Drupal 8. You can configure them at `/admin/structure/display-modes/view`. There are view modes for Content Types, Paragraph Types,

# Frontend Development
I will do my best to consistently use the terms "Design Pattern" and "Component" non-interchangeably to describe two different things. Out in the world, you'll hear the word "component" used to describe twelve different things, and it's very hard to determine what's being discussed.

I find it helpful to differentiate between a "Design Pattern" and a "Component".

## Definitions
**A Component** is the structure of the thing. Think of it like the type of wood you choose to make cabinets out of, the nails you use to hold it together, and the structural design - like how many shelves are they going to have, and whether or not there will be pull-out drawers.

In frontend speak, this is the collection of html elements used to make a component. e.g. A heading, an image, some text, and an a tag that wraps the whole thing to make it all link somewhere.

**A Design Pattern** is the look and feel of the thing. So, the paint color or stain for the cabinets, the position of the shelves (if they're movable), the hardware, like the handles. These things don't affect how the cabinets hold food, they just make them visually appealing, and look like cabinets.

In the frontend world, this is typically the css (though you can have twig files for specific design patterns.) These will define font sizes, the colors, the layout.

A great example of this distinction is the Card component, and the associated design patterns in Pattern Lab.

The card component is at `02-molecules/card/card.twig`. In it you'll see some wrapper divs, but also places for an image, a heading, some text, and a link. This is the card component.

At `http://localhost:3000/pattern-lab/public/?p=viewall-molecules-card` you'll see all of the Card design patterns. These are implemented via `.yml` files in Pattern Lab, and each of these defines a distinct design pattern for different use cases. At small screen sizes some of them are very similar, like the basic "Card" and the "Card List" components. And the "Card Featured" and "Card Single" components, but as the browser scales, you'll notice the layout is different on large screens. The "Card Featured and "Card Event" are also very similar, but the Event variation needs a slightly different treatment of the subhead (the date) to make it pop a little more, so it's a different Design Pattern.

The tricky thing is that sometimes you'll hear someone say "the card component" when they're talking about the group of card design patterns. I'll try not to do that. :)

## Pattern Lab
### How to view it
#### Live changes:
- In a terminal move into the theme directory `cd web/themes/custom/uft/`
- start the theme scripts `npm start` or `yarn start` (yarn is preferred)
- Visit one of the URLs provided `http://localhost:3000/pattern-lab/public/` or the one specific to your machine's local IP. (The local IP version can be accessed by other devices on the same local network. e.g. phones and tablets).

#### Multidevs and "compiled" version
- Tack http://uftwebsite.lndo.site/themes/custom/uft/pattern-lab/public/ on to the end of the site URL.

e.g. `http://uftwebsite.lndo.site/themes/custom/uft/pattern-lab/public/`
e.g. `http://dev-uft-website.pantheonsite.io/themes/custom/uft/pattern-lab/public/`

## Creating Components and Using Existing Components
The components are in `/themes/custom/uft/components/_patterns`

Add and modify any .twig, _.scss, or .js files you need.

### Create new component from existing ones
#### Atoms
Atoms are typically single html elements. These are rarely implemented directly by a Drupal template. Their main function is to provide consistent base styles from which to construct "display components."

For example:
- `01-atoms/00-links/link/link.twig` is an `a` tag.
- `01-atoms/01-text/text/paragraph.twig` is a `p` tag.
- `01-atoms/01-text/text/text.twig` is a `div` tag.

**Use**
The `node--article--simple-list.html.twig` template uses the `simple-list-item.twig` atom to display article titles in a list.

#### Molecules
Molecules are made up of atoms, and sometimes, other molecules.

For example:
- `02-molecules/content-image/content-image.twig` contains 3 atoms: `image`, `text`, and `text`.
- `02-molecules/text-with-image/text-with-image.twig` contains one molecule, and one atom: `content-image` and `text.twig`. It's basically, and image, and two text fields, but because the image has optional credit and captions, we use that existing molecule, instead of re-creating that markup here.

**Use**
The `node--article--teaser-list.html.twig` template implements the `card.twig` component.
The `node--article--full.html.twig` template actually uses a couple molecules. One for the heading, one for the print and email actions, and then, you'll notice a variable `content|without(...)` This is a Drupal specific Twig filter that allows you to, in this case, show the content without certain sub-elements. We want to exclude the ones we used above so that they aren't printed to the screen twice. This is useful when you don't know which fields might be added, and you want to allow them all to display in the order they are in the Drupal interface.

#### Organisms
Organisms relate most closely to "Content Types" and "Views". They're the most complex design patterns containing multiple atoms, and molecules. They _RARELY_ contain other organisms, and when they do, it's usually just for demonstration in PL. (e.g. Wrapped by a Twig block.)

The patterns designed for Views always contain at least one Twig block that is replaced in the drupal template with the content of a view. This is because the results of the view are styled using their own components. (e.g. the card pattern, the search result pattern, etc.)

In these cases, the Organism template might really just contain a wrapper element, maybe a heading and a "more" link, and then a Twig block for the actual content of the component.

For example:
- `03-organisms/flexible-grid/flexible-grid.twig` has a wrapping div, a molecule, an atom (for the headign and link, respectively), and a Twig block for the actual content of the grid.

This is implemented in the `paragraph--featured-content.html.twig` file. In that you'll see that we're mapping Drupal data to the heading, subheading, link, and also defining the color scheme based on a select box. Then we're passing the `field_view` field into the Twig block unaffected.

The `views-view-unformatted--featured-content.html.twig` file is where the grid content calculations etc. are happening. Then in that Twig block we're again, just passing in `row.content` unaffected.

Finally, the `node--card-third.html.twig` file is where any node that's displayed using the `card-third` view mode is configured. The featured content view displays content with this view mode by default, so this file is where you see the Drupal node data like `label`, `content.field_summary`, `url`, etc. passed into the card template.

So this demonstrates how the layers of Drupal templates map to the atomic design patterns of the pattern library.

#### Templates
These are the "page" level layout templates. There are currently two: `_default.twig` is for pages with sidebars, and `_section-page-base.twig` is for full-width pages.

The `full-width.twig` and `with-sidebar.twig` files are just for demonstration in Pattern Lab, and the `_place-holder.twig` file is also just for PL. That's what we use to create the dotted line placeholders in PL.

You'll notice that the `page.html.twig` Drupal template simply includes the `_default.twig` file and the `page--node--section-page.html.twig` file simply includes the `_section-page-base.twig` file.

#### Pages
These files are all strictly for Pattern Lab demonstration. They will never be included by a Drupal template. To emphasize that, we actually write all of the content directly into the twig file (no .yml files).

These can be created liberally to demonstrate different content layout possibilities. They are highly useful when creating new components because they show how that new component will interact with other content on the page. Doing so will often reveal layout and spacing issues that weren't considered when the component was being created in isolation.

### Create brand new component
To create new components, you just write the twig file, and any necessary scss and js files. You will need to stop and restart the `start` command in order to pick up _new_ files, but changes will be auto-refreshed.

## Twig
Like you've already seen, Twig files are basically HTML files that support things like variables, and including other twig files. A Twig file could be entirely vanilla HTML, entirely Includes, Extends, or Embeds or a combination.

## Sass workflow
There is a `start` command defined in the theme's `package.json` file that runs the build scripts, and starts a browsersync session where you can view the current code, and any changes in real time.

### scss conventions
We're using sass globbing, so any file that starts with an underscore will be concatenated into the style.scss file, and written out to css.

There's nothing special about the sass, it's very standard.

I do follow a convention of nesting the parts of BEM classes.

e.g.
```
.card--basic {
  ... stuff 1 ...

  &__heading {
    ... stuff 2 ...
  }

  &__content {
    ... stuff 3 ...
  }
}
```

That will compile to:

.card--basic {... stuff 1 ...}
.card--basic__heading {... stuff 2 ...}
.cad--basic__content {... stuff 3 ...}

### Sass variables
The `00-base` folder has all of the site-wide variables. Like font-sizes, colors, spacing variables, site-wide layout breakpoints, etc. There's actually not a whole lot in here, because we also create component-specific variables and breakpoints as needed.

For example, the `_flexible-grid.scss` file has a couple of breakpoints defined at the top, which are re-used throughout that file. The colors are only used in one place in this file, so they're just written inline.

The `_link.scss` file has a color defined at the top, because it's re-used multiple times throughout the file, and I just want to be able to change it in one place, and know that it's updated everywhere it should be.

## Bem and other functions
### What is BEM?
The BEM function is something we (Jeff) created and contributed back to the community that supports easily creating BEM style class names in Twig components.

### Why BEM?
It makes component-based styling much easier.

## Libraries (attach_library)
The attach_library function is a Drupalism that we've back-ported (more simplified) to Pattern Lab so that we can write code one way and in one place, and have it work everywhere.

### Defining libraries
Libraries are defined in `uft.libraries.yml`. They are named, and contain the css and js that are needed for certain sections of the site.

In `03-organisms/accordion/accordion.twig` you can see how we add a library with the `{{ attach_library('uft/LIBRARY_NAME') }}` syntax.

## Javascript
### Writing JS to work in both PL and Drupal
The `03-organisms/slideshow/slideshow.js` file shows how to write Drupal specific JS for when you need to use Drupal behaviors. The `03-organisms/accordion/accordion.js` file shows vanilla JS that will work in both places too.

# Styling Content
## Content
-
## Paragraphs
-
## View Modes
-
## Templates
-


# Configuration management and feature development
## Exporting config
-
