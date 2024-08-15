# Setting up Tailwind CSS in a React Project

## Getting Started

```bash
npx create-react-app react-tailwindcss
cd react-tailwindcss
npm install tailwindcss postcss-cli autoprefixer -D
```
To initialize Tailwind CSS by creating the default configration:

```bash
npx tailwind init tailwind.js --full
```

## Configure Tailwind CSS

```js
module.exports = {
    content: [
        './src/**/*.{js,jsx,ts,tsx}',
    ],
    ...
};
```

## Configure PostCSS

According to PostCSS documentation:

> PostCSS is a tool for transforming styles with JS plugins. These plugins can lint your CSS, support variables and mixins, transpile future CSS syntax, inline images, and more.

### WHY AUTOPREFIXER?

It's necessary to install Autoprefixer along with Tailwind CSS because Autoprefixer usually tracks [caniuse.com](https://caniuse.com) to see which CSS properties need to be prefixed. Hence, Tailwind CSS does not provide any vendor prefixing. 

Create a PostCSS configuration file in the base directory:

```bash
touch postcss.config.js
```

Then add the following code to it:

```js
const tailwindcss = require('tailwindcss');

module.exports = {
    plugins: [
        tailwindcss('./tailwind.js'),
        require('autoprefixer'),
    ],
};
```

## Inject Tailwind's Components, Utilities and Base Styles

Inside the <code>src</code> folder create a folder, name it <code>assets</code>, this is where all the styles would be stored. In that folder, create a *tailwind.css* and *main.css* file respectively. The *tailwind.css* file will be used to import Tailwind CSS styles, and for custom configurations and rules. The *main.css* will hold the styles that are generated as a result of what we have in the *tailwind.css* file.

### Import the base styles and configurations

```css
 /* /src/assets/tailwind.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

- <code>@tailwind base</code><br>
This injects Tailwind's base styles, which is a combination of <code>Normalize.css</code> and some additional base styles.
- <code>@tailwind components</code><br>
This injects any component (reusable styles like cards and form elements, etc) classes registered by plugins based in our config file.
- <code>@tailwind utilities</code><br>
This injects all of Tailwind's utility classes (including the default and your own utilities) generated based on the config file.

If you're using <code>postcss-import</code>, use this instead:

```cs
@import 'tailwindcss/base';
@import 'tailwindcss/components';
@import 'tailwindcss/utilities';
```

## Configure the App to Build the CSS

Open <code>package.json</code> and use the snippet below in place of the script part:

```json
"script": {
    "start": "npm run watch:css && react-scripts start",
    "build": "npm run watch:css && react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject",
    "watch:css": "postcss src/assets/tailwind.css -o src/assets/main.css"
}
```

## Importing Our CSS

```js
// src/index.js
// import './index.css'; // comment out this line
import './assets/main.css';
```

## Build a Profile Card Project

```bash
npm start
```

Before we start, put an image file <code>profile.jpg</code> in the <code>src</code> folder, then modify the <code>src/App.js</code> file to add some basic structure, without any CSS for now.

Then, add some styles to the elements:

### First <code>div</code>

```js
<div className="max-m-sm rounded overflow-hidden shadow-lg">
    <img className="w-full" src="profile.jpg" alt="Display" />
```

- <code>max-m-sm</code> - The maximum width on small screens
- <code>rounded</code> - Adds rounded corners
- <code>overflow-hidden</code> - Hides any overflow content
- <code>shadow-lg</code> - Adds a large shadow
- <code>w-full</code> - Sets the width to 100%

### Second <code>div</code>

```js
<div className="px-6 py-4">
```

- <code>px-6</code> - Adds 1.5rem (24px) of horizontal padding
- <code>py-4</code> - Adds 1rem (16px) of vertical padding

### Third <code>div</code>

```js
<div className="font-bold text-purple-500 text-xl mb-2">
    Blessing Krofegha
</div>
<p className="text-gray-700 text-base">
    When I'm not coding, I switch to NetFlix with biscuits and cold tea as my companion. <span></span>😜
</p>
```

- <code>font-bold</code> - Sets <code>font-weight</code> to a value of <code>700</code>
- <code>text-purple-500</code> - Sets the text color to light purple
- <code>text-xl</code> - Sets the text size to extra-large
- <code>mb-2</code> - Adds 0.5rem (8px) of bottom margin
- <code>text-gray-700</code> - Sets the text color to gray, is equal <code>color: #4a5568</code>
- <code>text-base</code> - Sets the text size to base, is equal <code>font-size: 1em</code> or <code>font-size: 1rem</code>

### Fourth <code>div</code>

```js
<div className="px-6 py-4">
    <span className="inline-block bg-gray-200 rounded-full px-3 py-1 text-sm font-semibold text-gray-700 mr-2">#Software Engineer</span>
    <span className="inline-block bg-gray-200 rounded-full px-3 py-1 text-sm font-semibold text-gray-700 mr-2">#Writer</span>
    <span className="inline-block bg-gray-200 rounded-full px-3 py-1 text-sm font-semibold text-gray-700 mt-2 ml-20">#Public Speaker</span>
</div>
```

- <code>rounded-full</code> - Is equal <code>border-radius: 9999px</code>
- <code>text-sm</code> - To make the <code>font-size</code> to small

## Optimization for Production

Use *purge* to minify the CSS size.

```bash
npm i @fullhuman/postcss-purgecss
```

Then, add the following in <code>postcss.config.js</code>:

```js
module.exports = {
    plugins: [
        require('tailwindcss'),
        require('autoprefixer'),
        require('@fullhuman/postcss-purgecss')({
            content: [
                './src/**/*.js',
                './public/index.html',
            ],
            defaultExtractor: content => content.match(/[A-Za-z0-9-_:/]+/g) || [],
        }),
    ],
};
```

In the above code, we use <code>defaultExtractor</code> to tell <code>purgecss</code> how to find unused css classes and remove them, by passing a function that retrieves the contents of the file and returns classnames that it finds in that file using a <code>RegEx</code>.

Using the <code>RegEx</code>, we check to see it the content found matches a pattern (classes with uppercase, lowercase, numbers, underscores, colons, and forward slashes), and if there isn't a match, return an empty array.

Next:

```bash
npm run build
```
