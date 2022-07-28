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

## Configure Environment Variables

1. We need to add the following environment variables:

```
# For Next
NEXT_PUBLIC_SANITY_PROJECT_ID=""
NEXT_PUBLIC_SANITY_DATASET="production"
SANITY_API_TOKEN=""
SANITY_PREVIEW_SECRET=""

# For Studio
SANITY_STUDIO_API_PROJECT_ID=""
SANITY_STUDIO_API_DATASET="production"
```

Both PROJECT*ID*\* values are the Sanity project ID (from the dashboard).

2. From the Settings->API tab, add a new API Token. Use 'blog' for the name and select the 'Editor' permissions option. Save the token to 'SANITY_API_TOKEN'.

## Start the Sanity Studio

1. In the 'studio' folder, run:

```
sanity start
```

1. If compiled successfully, the message tells you to go to http://localhost:3333. Login with the same method that was used to create your Sanity account. This connects you to a 'dev server' - or Studio home page.

## Add a Post

1. From the Studio home page we launched in the last step click 'Post'.
2. Click the 'pencil' icon (top left corner) to bring up a form with the fields we defined in the schema.
3. For the 'slug', we just entered 'hello-world'.
4. Click the green 'Publish' button when you're finished.
5. Click the 'Content' panel on the left - the new post should be there.

## Show the Posts on the Website

1. Install the next-sanity library.

```
npm install next-sanity
```

2. Add a Sanity client handler in 'lib/sanity.js'.
3. Import this client into 'index.js':

```
import { client } from 'lib/sanity';
```

4. Add a 'getStaticProps()' method to 'index.js' - this is used for pre-rendering data at build time. Here we'll use the Sanity client to fetch the lists of posts from Sanity.

## Add the Single Post Page

1. Import 'next/link' and add the slug link to each entry in 'index.js'.
2. Create a posts folder in 'pages' and a 'pages/posts/[slug].js'. This file uses 'getStaticPaths': "This is a dynamic route ([slug].js), and since we want to generate this page at build time, we need to get all the possible paths for this page."
3. In 'getStaticProps' we get the single blog post by adding the slug information from the page parameters.
4. NOTE: In the '[slug].js' page, the temporary paragraph for showing the body doesn't show - I changed the text to black to make it show - the real body text will be added next. UPDATE: it was the black background and white text style carried over from the last lesson... fixed!

```
<p className="text-black">Body Goes Here</p>
```

## Render the Post Body

1. This has to be done via a library since the body is a rich-text object:

```
npm install @portabletext/react
```

2. Import the 'PortableText' library in 'index.js' and then use that on the body.
3. Theoretically, if you add another post (and don't forget to set the date), it's automatically picked up by what's running on port 3000 - seems like I needed to refresh the page to get them to show.

## Deploy to Vercel

1. This took a bit of work - in order for your repository to be visible to Vercel, you need to either make all repositories visible or selected repositories visible. I have set it up so only selected repositories are visible (forgot about this). To add an additional repository, go to Settings under your github account (not on a individual repository). Under Settings, find Applications. I have Netlify and Vercel setup. You need to click the Configure button on Vercel to add an additional repository.
2. I then had issues with Vercel giving me a 404 after I imported the repository. I logged out and then back in - all then worked.
3. We added the environment variables from the .env file - and then clicked Deploy. So far so good.
4. Click on 'Go to Dashboard' - there you'll find the URL for the app.

## Configure Sanity to Run on Vercel

1. Just to be clear, we have a Sanity client running on port 3033 - that's working along with the app running on port 3000. We need to have Sanity running in conjunction with Vercel.
2. First, run the following:

```
sanity deploy
```

I used 'Bootcamp-Week15-blog' for the Studio hostname. Really not sure what this is supposed to be..
After completion:

```
Success! Studio deployed to https://bootcamp-week15-blog.sanity.studio/
```

As indicated in the lesson: "And the studio just works. But you might want to have it run on your own domain.".

3. Add a 'vercel.json' to the project folder with the following contents:

```
{
  "rewrites": [{ "source": "/studio/(.*)", "destination": "/studio/index.html" }]
}
```

4. In 'studio/sanity.json' add the basePath property in this way:

```
....
{
  "root": true,
  "project": {
    "name": "blog",
    "basePath": "/studio"
  },
  ...
```

5. Add your Vercel app domain to white list it by running the command - replace the example here with the domain of the app...

```
sanity cors add https://blog-example-sanity.vercel.app --credentials
```

6. Generate the public 'studio' folder:

```
cd studio
sanity build ../public/studio -y;
```

7. Commit and push the changes and after the Vercel rebuild you should see the studio on '/studio'.
