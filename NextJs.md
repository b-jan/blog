# Next.js: Use Server-Side Rendering in your React App 

![Next.js](assets/ssr-next.jpg?raw=true "React SSR with Next.js")

Usually, we are completely running React.js on client-side: Javascript is interpreted by your browser.
For this reason, client-side rendering is the simplest setting for your React application.
The initial html rendered by the server is a placeholder `<div id="root"></div>`.
Then the entire UI is rendered in the browser once all your scripts load.

The problem is that, in the meantime, your visitor sees... nothing, a blank page!

Looking for how to get rid of this crappy blank page for a personal project, I discovered Next.js: the current best framework for making server-side rendering React applications.


### Why SSR (Server-Side Rendering)?

This is not the point of this article, but here is a quick sum-up of what SSR can bring to your application:

- Improve your SEO
- Speed up your first page load
- Avoid blank page flicker before rendering

If you want to know more about it, please read this great article:
[Benefits of Server Side Over Client Side Rendering](https://medium.com/walmartlabs/the-benefits-of-server-side-rendering-over-client-side-rendering-5d07ff2cefe8).

Let's focus on the how rather than the why here.


### What's the plan?

Facebook has created a node module [create-react-app](https://github.com/facebook/create-react-app) to easily generate a React application.
Many React applications are close to its settings. That's why I chose it as a code base for this article.

This article is split in 3 short parts:

- How to upgrade manually your React app to SSR
- How to start with Next.js from scratch
- How to bring Next.js to your existing React app

I won't go through all the steps, but I will bring your attention on the main points of interesting.
I also provide a repository for each of the 3 strategies.


## 1) Look how twisted manual SSR is...

![Manually](assets/injury.jpg?raw=true "React SSR manually")

In that part, we will see how to implement SSR manually on an existing React app. Let's take the [create-react-app] starter code:

- `package.json` for dependencies
- Webpack configuration included
- `App.js` - loads React and renders the Hello component
- `index.js` - puts all together into a root component

### Checking rendering type

I just added to the code base a simple function `isClientOrServer` based on the availability of `window`:

```
const isClientOrServer = () => {
  return (typeof window !== 'undefined' && window.document) ? 'client' : 'server';
};
```

so that we display on the page what is rendering the application: server or client.

To test it by yourself:

- clone [this repository](https://github.com/b-jan/manual-react-ssr) I made.
- checkout the initial commit
- install the dependencies with `yarn`
- launch the dev server with `yarn start`
- browse to http://localhost:3000 to view the app

I am now simulating a '3G network' in Chrome (could be a real life usage) so that we really understand what is going on.
An here is the result:

![CSR](assets/cra-csr.gif?raw=true "Client-side rendering on a create-react-app")

### Implementing SSR

Let's remove that crappy flickering with server-side rendering!
I won't show all the code (check my repo to see it in details) but here are the main steps.

We first need a server, add Express to our dependencies with `yarn add express`.

Webpack only loads the src/ folder, we can thus create a new folder named server/ next to it.
Inside, create a file `index.js` where we use express and a server renderer.

```
// use port 3001 because 3000 is used by our front
const PORT = 3001;
const path = require('path');

// initialize the application and create the routes
const app = express();
const router = express.Router();

// root (/) should always serve our server rendered page
router.use('^/$', serverRenderer);
```

To render our html, we use a server renderer that is replacing the root component with the built html:

```
// index.html file created by create-react-app build tool
const filePath = path.resolve(__dirname, '..', '..', 'build', 'index.html');

fs.readFile(filePath, 'utf8', (err, htmlData) => {
  // render the app as a string
  const html = ReactDOMServer.renderToString(<App />);

    // inject the rendered app into our html
    return res.send(
      htmlData.replace(
        '<div id="root"></div>',
        `<div id="root">${html}</div>`
      )
    );
  }
```

We finally need an entry point that will tell Node how to interpret our React JSX code. We achieve this with Babel.

```
require('babel-register')({
  ignore: [ /(node_modules)/ ],
  presets: ['es2015', 'react-app']
});
```

To test it by yourself:

- checkout last changes on master branch
- install the dependencies with `yarn`
- build the application with `yarn build`
- declare babel environment in your terminal: `export BABEL_ENV=development`
- launch your node server with `node server/bootstrap.js`
- browse to http://localhost:3001 to view the app

Still simulating the '3G network' in Chrome, here is the result:

![SSR](assets/cra-ssr.gif?raw=true "Server-side rendering on a create-react-app")

Do not be mistaken, the page is rendered by server. But as soon as the javascript is fully loaded,
window.document is available and the `isClientOrServer()` function returns `client`.

We proved that we can do SSR, but what the heck with that React logo?

### We miss many features

Our example is a good proof of concept but very limited. We would like to see more feature like:

- import images in js files (logo problem)
- several routes usage or route management (check [this article](https://medium.com/@benlu/ssr-with-create-react-app-v2-1b8b520681d9))
- deal with the `</head>` and the metatags
- code splitting (here is [an article](https://medium.com/bucharestjs/upgrading-a-create-react-app-project-to-a-ssr-code-splitting-setup-9da57df2040a
) solving the problem)
- manage the state of our app or use Redux (check this [great article](https://medium.com/@cereallarceny/server-side-rendering-with-create-react-app-fiber-react-router-v4-helmet-redux-and-thunk-275cb25ca972)
)

It is possible to make SSR work perfectly on top of create-react-app, we won't go through all the painful work in this article.
Still, if you're interested in it, I attached just above some great articles giving detailed explanations.

Seriously... Next.js can bring you all these features!


## 2) Next.js helps you building server rendered React.js Application

![Easy Next](assets/cucumber.jpg?raw=true "Easy SSR with Next.js")

### What is Next.js?

Next.js is a minimalistic framework for server-rendered React applications with:

- a very simple page based routing
- Webpack hot reloading
- automatic transpilation (with babel)
- deployment facilities
- automatic code splitting (loads page faster)
- built in css support
- simple integration with Redux using next-redux-wrapper.

### Get started in 1 minute

In this short example, we are going to see how crazy simple it is to have a SSR app ready with Next.js.

First, generate your package.json with `npm init` and install Next.js with `npm install --save next react react-dom`.
Then, add a script to your package.json like this:

```
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
```

Create a pages/ folder. Every .js file becomes a route that gets automatically processed and rendered.
Add a index.js file in that pages/ folder (with the execution of our `isClientOrServer` function):

```
const Index = ({ title = 'Hello from Next.js' }) => (
  <div>
    <h1>{title}</h1>
    <p className="App-intro">
      Is my application rendered by server or client?
    </p>
    <h2><code>{isClientOrServer()}</code></h2>
  </div>
);

export default Index;
```

No need to import any library at the top of our index.js file, Next.js already knows that we are using React.

Now enter ```npm run dev``` into your terminal and go to http://localhost:3000: Tadaaaaa!

![Next Hello World](assets/next.gif?raw=true "Next.js Hello World Result")

Repeat the same operation inside your pages/ folder to create a new page.
The name you give to the file will directly be matched by the url to access it.

You're ready to go! You're already doing SSR.

### Use create-next-app

You want to start a server-side rendered React app, you can now stop using create-react-app,
and start using [create-next-app](https://github.com/segmentio/create-next-app):

```
npm install -g create-next-app

create-next-app my-app
cd my-app/
npm run dev
```

This is all you need to do to create a React app with server-side rendering thanks to Next.js.

Better than a simple Hello World app, check this [Hacker News clone](https://github.com/now-examples/next-news) implementing Next.js.
It is fully server-rendered, queries the data over Firebase and updates in realtime as new votes come in.


## 3) How to use the power of Next.js in my existing React.js application?

![Trick](assets/tunnel.jpg?raw=true "Next.js in existing app")

Well, now you know that if you start from scratch and you want to make a SSR React app, Next.js might be the best choice.
But what if your React app is already 6 months old? How to migrate your app to Next.js?

Exactly like how we started with Next.js from scratch, add the Next.js dependency with `yarn add next`.
Then replace the scripts in your `package.json`

```
  "scripts": {
    "dev": "next dev src",
    "start": "node server.js",
    "build": "next build src",
    "test": "react-scripts test --env=jsdom"
  }
```

We need a node HTTP server and we now use the Next.js library to build and develop our application.
We can keep `react-scripts` to run our tests.

Run your Next.js dev server with `yarn dev` and see this beautiful error:

![404 page](assets/404.jpg?raw=true "Next starting error")

Let's say we have a React Redux application with such a structure:

```
src/
  actions/
  assets/
    css/
    font/
    img/
    favicon.ico
    index.html
  components/
    Header.component.js
    Homepage.component.js
    Footer.component.js
  containers/
    App.container.js
    Header.container.js
    Footer.container.js
  reducers/
  store/
  translations/
  index.js
  routes.js
package.json
README.md
yarn.lock
```

The strategy is to keep the same file structure we have in our client-side project and simply add a
pages/ folder and a static/ folder (instead of assets/ folder in our case).

About how to migrate, you can create a single Next.js page with your React Router components and then gradually
migrate each route to a Next.js one and remove the React Router route.

First make it work as a SPA, then progressively break up into pages.

https://gist.github.com/jaredpalmer/a73bc00cac8926ff0ad5281879b1eb90







## Improving again


If you want to know more, visit : https://learnnextjs.com/basics/getting-started
Vue.js has its own Next.js named Nuxt.js, visit : https://nuxtjs.org/guide
Caching
Better performance for end users, Better customer engagement



### Do both

Here, the initial render is on the server. Hence, the html received by the browser has the UI as it should be.
Once the scripts are loaded, the virtual DOM is re-rendered once again to set up your components' event handlers.

Over here, you need to make sure that you re-render the exact same virtual DOM (root ReactJS component) with
the same props that you used to render on the server. Otherwise, ReactJS will complain that the server-side and
client-side virtual DOMs don't match.


### Pre-rendering

https://medium.com/@dtinth/prerendering-a-create-react-app-app-6e8571d7d662

### Caching


### Vue.js and Nuxt

