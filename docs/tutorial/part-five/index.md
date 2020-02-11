---
title: Source Plugins
typora-copy-images-to: ./
disableTableOfContents: true
---

> এই টিউটোরিয়ালটি গ্যাটসবির ডেটা স্তর সম্পর্কে একটি সিরিজের অংশ। এখানে চালিয়ে যাওয়ার আগে আপনি [অংশ ৪] (/ টিউটোরিয়াল / অংশ-চতুর্থ /) পেরিয়ে গেছেন তা নিশ্চিত করুন।

## এই টিউটোরিয়ালে কি আছে?

এই টিউটোরিয়ালে, আপনি গ্রাফকিউএল এবং উত্স প্লাগইনগুলি ব্যবহার করে কীভাবে আপনার গ্যাটসবি সাইটে ডেটা টানবেন সে সম্পর্কে শিখবেন। যাইহোক, এই প্লাগইনগুলি সম্পর্কে জানার আগে, আপনি গ্রাফিকিউএল নামক কোনও জিনিস কীভাবে ব্যবহার করবেন তা জানতে চাইবেন, এটি এমন একটি সরঞ্জাম যা আপনাকে আপনার প্রশ্নগুলি সঠিকভাবে গঠনে সহায়তা করে।

## উপস্থাপক GraphiQL

গ্রাফিক্যুয়াল হ'ল গ্রাফকিউএল ইন্টিগ্রেটেড ডেভলপমেন্ট এনভায়রনমেন্ট (আইডিই)। এটি একটি শক্তিশালী (এবং চারপাশে দুর্দান্ত ভয়ঙ্কর) সরঞ্জাম যা আপনি গ্যাটসবি ওয়েবসাইটগুলি তৈরি করার সময় প্রায়শই ব্যবহার করবেন।

You can access it when your site's development server is running—normally at
<http://localhost:8000/___graphql>.

<video controls="controls" autoplay="true" loop="true">
  <source type="video/mp4" src="/graphiql-explore.mp4"></source>
  <p>Your browser does not support the video element.</p>
</video>

অন্তর্নির্মিত `Site` "type"এর আশেপাশে পোকে দিন এবং এতে কোন ক্ষেত্রগুলি উপলব্ধ রয়েছে তা দেখুন -- including the `siteMetadata` object you queried earlier. গ্রাফিকিউএল খোলার চেষ্টা করুন এবং আপনার ডেটা দিয়ে খেলুন! Press <kbd>Ctrl + Space</kbd> (or use <kbd>Shift + Space</kbd> as an alternate keyboard shortcut) to bring up the autocomplete window and <kbd>Ctrl + Enter</kbd> to run the GraphQL query. You'll be using GraphiQL a lot more through the remainder of the tutorial.

## Using the GraphiQL Explorer

The GraphiQL Explorer enables you to interactively construct full queries by clicking through available fields and inputs without the repetitive process of typing these queries out by hand.

<EggheadEmbed
  lessonLink="https://egghead.io/lessons/gatsby-build-a-graphql-query-using-gatsby-s-graphiql-explorer"
  lessonTitle="Build a GraphQL Query using Gatsby’s GraphiQL Explorer"
/>

## উত্স প্লাগইন

Data in Gatsby sites can come from anywhere: APIs, databases, CMSs, local files, etc.

Source plugins fetch data from their source. E.g. the filesystem source plugin knows how to fetch data from the file system. The WordPress plugin knows how to fetch data from the WordPress API.

Add [`gatsby-source-filesystem`](/packages/gatsby-source-filesystem/) and explore how it works.

First, install the plugin at the root of the project:

```shell
npm install --save gatsby-source-filesystem
```

Then add it to your `gatsby-config.js`:

```javascript:title=gatsby-config.js
module.exports = {
  siteMetadata: {
    title: `Pandas Eating Lots`,
  },
  plugins: [
    // highlight-start
    {
      resolve: `gatsby-source-filesystem`,
      options: {
        name: `src`,
        path: `${__dirname}/src/`,
      },
    },
    // highlight-end
    `gatsby-plugin-emotion`,
    {
      resolve: `gatsby-plugin-typography`,
      options: {
        pathToConfigModule: `src/utils/typography`,
      },
    },
  ],
}
```

Save that and restart the gatsby development server. Then open up GraphiQL again.

In the explorer pane, you'll see `allFile` and `file` available as selections:

![graphiql-filesystem](graphiql-filesystem.png)

Click the `allFile` dropdown. Position your cursor after `allFile` in the query area, and then type <kbd>Ctrl + Enter</kbd>. This will pre-fill a query for the `id` of each file. Press "Play" to run the query:

![filesystem-query](filesystem-query.png)

In the Explorer pane, the `id` field has automatically been selected. Make selections for more fields by checking the field's corresponding checkbox. Press "Play" to run the query again, with the new fields:

![filesystem-explorer-options](filesystem-explorer-options.png)

Alternatively, you can add fields by using the autocomplete shortcut (<kbd>Ctrl + Space</kbd>). This will show queryable fields on the `File` nodes.

![filesystem-autocomplete](filesystem-autocomplete.png)

Try adding a number of fields to your query, pressing <kbd>Ctrl + Enter</kbd>
each time to re-run the query. You'll see the updated query results:

![allfile-query](allfile-query.png)

The result is an array of `File` "nodes" (node is a fancy name for an object in a
"graph"). Each `File` node object has the fields you queried for.

## Build a page with a GraphQL query

Building new pages with Gatsby often starts in GraphiQL. You first sketch out
the data query by playing in GraphiQL then copy this to a React page component
to start building the UI.

Let's try this.

Create a new file at `src/pages/my-files.js` with the `allFile` GraphQL query you just
created:

```jsx:title=src/pages/my-files.js
import React from "react"
import { graphql } from "gatsby"
import Layout from "../components/layout"

export default ({ data }) => {
  console.log(data) // highlight-line
  return (
    <Layout>
      <div>Hello world</div>
    </Layout>
  )
}

export const query = graphql`
  query {
    allFile {
      edges {
        node {
          relativePath
          prettySize
          extension
          birthTime(fromNow: true)
        }
      }
    }
  }
`
```

The `console.log(data)` line is highlighted above. It's often helpful when
creating a new component to console out the data you're getting from the GraphQL query
so you can explore the data in your browser console while building the UI.

If you visit the new page at `/my-files/` and open up your browser console
you will see something like:

![data-in-console](data-in-console.png)

The shape of the data matches the shape of the GraphQL query.

Add some code to your component to print out the File data.

```jsx:title=src/pages/my-files.js
import React from "react"
import { graphql } from "gatsby"
import Layout from "../components/layout"

export default ({ data }) => {
  console.log(data)
  return (
    <Layout>
      {/* highlight-start */}
      <div>
        <h1>My Site's Files</h1>
        <table>
          <thead>
            <tr>
              <th>relativePath</th>
              <th>prettySize</th>
              <th>extension</th>
              <th>birthTime</th>
            </tr>
          </thead>
          <tbody>
            {data.allFile.edges.map(({ node }, index) => (
              <tr key={index}>
                <td>{node.relativePath}</td>
                <td>{node.prettySize}</td>
                <td>{node.extension}</td>
                <td>{node.birthTime}</td>
              </tr>
            ))}
          </tbody>
        </table>
      </div>
      {/* highlight-end */}
    </Layout>
  )
}

export const query = graphql`
  query {
    allFile {
      edges {
        node {
          relativePath
          prettySize
          extension
          birthTime(fromNow: true)
        }
      }
    }
  }
`
```

And now visit [http://localhost:8000/my-files](http://localhost:8000/my-files)… 😲

![my-files-page](my-files-page.png)

## এরপরে কী আসছে?

এখন আপনি শিখেছেন কীভাবে উত্স প্লাগইনগুলি _ ইনটো_গ্যাটসির ডেটা সিস্টেমটি ডেটা নিয়ে আসে।পরবর্তী টিউটোরিয়ালে, আপনি শিখবেন কীভাবে ট্রান্সফর্মার প্লাগইনগুলি উত্স প্লাগইনগুলি নিয়ে আসা কাঁচামালকে _ ট্রান্সফর্ম_ করে।সোর্স প্লাগইন এবং ট্রান্সফর্মার প্লাগইনগুলির সংমিশ্রণ কোনও গাটস্বি সাইট তৈরির সময় আপনার প্রয়োজন হতে পারে এমন সমস্ত ডেটা সোর্সিং এবং ডেটা ট্রান্সফরমেশন পরিচালনা করতে পারে। ট্রান্সফরমার প্লাগইনগুলি সম্পর্কে জানুন [অংশ ছয় tutorial](/tutorial/part-six/).
