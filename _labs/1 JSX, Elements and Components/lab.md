# Lab 1: JSX, Elements & Components
In this series of labs, we'll build a simple web application that shows you some information about a car with a Dutch licence plate.

## Step 1: Basic setup
* Make sure to have [NodeJS](https://nodejs.org/en/) installed; the LTS release should be fine.
* Create a directory to put your work in, let's say "reactjs-labs"
* Open a terminal / command prompt and navigate to the folder you've just created
* Run `npm init -y`, followed by `npm install babel-cli@6 babel-preset-react-app@3`.
* Run `npx babel --watch src --out-dir . --presets react-app/prod`.
Note that this command does not complete - it will watch your `src/` directory for changes.
* Create a file, `index.html`, and insert `<script>` tags to load the development dependencies:
  * React: https://unpkg.com/react@16/umd/react.development.js
  * React DOM: https://unpkg.com/react-dom@16/umd/react-dom.development.js

## Step 2: Create your first element
* Copy `index.html` to the folder you've just created.
* In the `src/` folder, add a new file called `example.js`.
* In `index.html`, use a `<script>` tag to load `example.js`.
Note that you should should load it from the same directory as `index.html`, not from `src/`.
Why is that?
* Edit `example.js` and add the following snippet:

  const Example = () => <div className="greeting">Hello, world!</div>;
  ReactDOM.render(<Example />, document.getElementById('app'));

* Add an empty `<div>` element to the HTML file and give it `id="app"`.
* Open the HTML file in the web browser of your choice and see how you are greeted in style!
* Inspect the `example.js` file.
Can you make sense of it?

## Step 3: Composing a simple user interface
* First, take a piece of paper and a pen, and make yourself a sketch of what you would like your application to look like.
This is called a "wireframe" - it identifies the main parts of your application and how the user can interface with it.
* Now, make components for each of the main elements in your wireframe.
Give them a name that describes what their purpose is.
Their implementation can be as simple as an empty `<div>` element.
Also, assign each of these `<div>` elements a CSS class (hint: you don't use `class` for that) that matches the component name.
* Add a CSS file to your project (next to `index.html`) and reference it from the HTML file.
Add rules to the CSS file for every CSS class that you've just referenced, and give them some style rules so you can easily recognise them.
You can do this, for example, by giving each of them a background of different colour.
* Load the HTML file in your browser and see if the structure of the application matches the wireframe. 