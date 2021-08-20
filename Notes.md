## Pre-rendering and Data Fetching

getStaticProps Details

Here is some essential information you should know about getStaticProps.

### Fetch External API or Query Database

In `lib/posts.js`, we’ve implemented `getSortedPostsData` which fetches data from the file system. But you can fetch the data from other sources, like an external API endpoint, and it’ll work just fine:

```js
export async function getSortedPostsData() {
  // Instead of the file system,
  // fetch post data from an external API endpoint
  const res = await fetch("..");
  return res.json();
}
```

    Note: Next.js polyfills fetch() on both the client and server. You don't need to import it.

You can also query the database directly:

```js
import someDatabaseSDK from 'someDatabaseSDK'

const databaseClient = someDatabaseSDK.createClient(...)

export async function getSortedPostsData() {
  // Instead of the file system,
  // fetch post data from a database
  return databaseClient.query('SELECT posts...')
}
```

This is possible because getStaticProps only runs on the server-side. It will never run on the client-side. It won’t even be included in the JS bundle for the browser. That means you can write code such as direct database queries without them being sent to browsers.
Development vs. Production

    In development (npm run dev or yarn dev), getStaticProps runs on every request.
    In production, getStaticProps runs at build time. However, this behavior can be enhanced using the fallback key returned by getStaticPaths

Because it’s meant to be run at build time, you won’t be able to use data that’s only available during request time, such as query parameters or HTTP headers.
Only Allowed in a Page

getStaticProps can only be exported from a page. You can’t export it from non-page files.

One of the reasons for this restriction is that React needs to have all the required data before the page is rendered.

### What If I Need to Fetch Data at Request Time?

Static Generation is not a good idea if you cannot pre-render a page ahead of a user's request. Maybe your page shows frequently updated data, and the page content changes on every request.

In cases like this, you can try Server-side Rendering or skip pre-rendering. Let’s talk about these strategies before we move on to the next lesson.

## Fetching Data at Request Time

If you need to fetch data at request time instead of at build time, you can try Server-side Rendering:

To use Server-side Rendering, you need to export getServerSideProps instead of getStaticProps from your page.
Using getServerSideProps

Here’s the starter code for getServerSideProps. It’s not necessary for our blog example, so we won’t be implementing it.

```js
export async function getServerSideProps(context) {
  return {
    props: {
      // props for your component
    },
  };
}
```

Because getServerSideProps is called at request time, its parameter (context) contains request specific parameters.

You should use getServerSideProps only if you need to pre-render a page whose data must be fetched at request time. Time to first byte (TTFB) will be slower than getStaticProps because the server must compute the result on every request, and the result cannot be cached by a CDN without extra configuration.
Client-side Rendering

If you do not need to pre-render the data, you can also use the following strategy (called Client-side Rendering):

    Statically generate (pre-render) parts of the page that do not require external data.
    When the page loads, fetch external data from the client using JavaScript and populate the remaining parts.

# Dynamic Routes

We’ve populated the index page with the blog data, but we still haven’t created individual blog pages yet (here’s the desired result). We want the URL for these pages to depend on the blog data, which means we need to use dynamic routes.
What You’ll Learn in This Lesson

In this lesson, you’ll learn:

    How to statically generate pages with dynamic routes using **getStaticPaths** .
    How to write getStaticProps to fetch the data for each blog post.
    How to render markdown using remark.
    How to pretty-print date strings.
    How to link to a page with dynamic routes.
    Some useful information on dynamic routes.
