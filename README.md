# Blog Website

This week's project is to create a website for content management. We’ll create a content-centered website that will use a headless CMS as its content “base”. From the lesson: And to make the editing experience great, so we have an easier time publishing, and other people can contribute to the blog without being developers, we’ll use Sanity.

Sanity is a “content platform”. They provide a content management system we can hook in our Next.js website.

## Initial Setup

1. Initial setup of Next.js is the same as prior weeks. The completed project for reference is here: https://github.com/flaviocopes/bootcamp-2022-week-15-blog
2. There is no authentication added to the project.

## Create a Simple Blog Design

1. Add some basic JSX to 'index.js' - basic web page with "Welcome to my blog" and some fake blog entries.

## Setup Sanity

1. Setup your account - sanity.io (using github).

## Configure Sanity

1. We need to install the Sanity CLI globally (?). Anyway, Flavio reminds us to do an NPM update every couple of weeks....

```
npm install -g @sanity/cli
```

1. Run init inside the project folder - note that this basically asks how you logged in, attempts that login. The remaining fields are as shown in the output - note that we changed the project output path to /blog/studio. Also, for this readme, I've **\*** out my path.....

```
sanity init

.....

? Project name: blog
Your content will be stored in a dataset that can be public or private, depending on
whether you want to query your content with or without authentication.
The default dataset configuration has a public dataset named "production".
? Use the default dataset configuration? Yes
✔ Creating dataset
? Project output path: ************/blog/studio
? Select project template Clean project with no predefined schemas
✔ Bootstrapping files from template
✔ Resolving latest module versions
✔ Creating default project files
```

3. Noted in the output:

```
First: cd ************/blog/studio - to enter project’s directory
Then: sanity start - to run Sanity Studio

Other helpful commands
sanity docs - to open the documentation in a browser
sanity manage - to open the project settings in a browser
sanity help - to explore the CLI manual
```

4. Once this is done, from '/studio', run 'studio manage' - this takes you to your Studio dashboard.

## Configure the Sanity Schema

1. Each blog entry will have a title, a content, a slug (the URL) and a published date.
2. In the file 'studio/schemas/schema.js', create a “post” type with some fields for title, slug, published date, and body (e.g. blog body).
3. In the same folder, add 'blockContent.js' - this is the rich content editor.
4. Neither of these changes are added to git, since they're in the /studio folder. Contents of 'studio/schemas/schema.js:

```
// First, we must import the schema creator
import createSchema from "part:@sanity/base/schema-creator";

// Then import schema types from any plugins that might expose them
import schemaTypes from "all:part:@sanity/base/schema-type";
import blockContent from "./blockContent";

// import the icon
import { DocumentIcon } from "@sanity/icons";

// Then we give our schema to the builder and provide the result to Sanity
export default createSchema({
  // We name our schema
  name: "default",
  // Then proceed to concatenate our document type
  // to the ones provided by any plugins that are installed
  types: schemaTypes.concat([
    {
      title: "Post",
      name: "post",
      icon: DocumentIcon,
      type: "document",
      fields: [
        {
          name: "title",
          title: "Title",
          type: "string",
        },
        {
          name: "slug",
          title: "Slug",
          type: "slug",
          options: {
            source: "title",
            maxLength: 96,
          },
        },
        {
          name: "publishedAt",
          title: "Published at",
          type: "datetime",
        },
        {
          name: "body",
          title: "Body",
          type: "blockContent",
        },
      ],
      preview: {
        select: {
          title: "title",
        },
      },
    },
    blockContent,
  ]),
});

```

And 'studio/schemas/blockContent.js':

```
/**
 * This is the schema definition for the rich text fields used for
 * for this blog studio. When you import it in schemas.js it can be
 * reused in other parts of the studio with:
 *  {
 *    name: 'someName',
 *    title: 'Some title',
 *    type: 'blockContent'
 *  }
 */
export default {
  title: "Block Content",
  name: "blockContent",
  type: "array",
  of: [
    {
      title: "Block",
      type: "block",
      // Styles let you set what your user can mark up blocks with. These
      // correspond with HTML tags, but you can set any title or value
      // you want and decide how you want to deal with it where you want to
      // use your content.
      styles: [
        { title: "Normal", value: "normal" },
        { title: "H1", value: "h1" },
        { title: "H2", value: "h2" },
        { title: "H3", value: "h3" },
        { title: "H4", value: "h4" },
        { title: "Quote", value: "blockquote" },
      ],
      lists: [{ title: "Bullet", value: "bullet" }],
      // Marks let you mark up inline text in the block editor.
      marks: {
        // Decorators usually describe a single property – e.g. a typographic
        // preference or highlighting by editors.
        decorators: [
          { title: "Strong", value: "strong" },
          { title: "Emphasis", value: "em" },
        ],
        // Annotations can be any object structure – e.g. a link or a footnote.
        annotations: [
          {
            title: "URL",
            name: "link",
            type: "object",
            fields: [
              {
                title: "URL",
                name: "href",
                type: "url",
              },
            ],
          },
        ],
      },
    },
    // You can add additional types here. Note that you can't use
    // primitive types such as 'string' and 'number' in the same array
    // as a block type.
    {
      type: "image",
      options: { hotspot: true },
    },
  ],
};

```
