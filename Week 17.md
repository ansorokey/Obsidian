## Dev Tools
The Chrome Dev Tools are a powerful and essential component of web development. It will be important to become familiar with them, how they work, and what they do. 

### Inspect Element
One of the more often used dev tools, which we can access by right-clicking the webpage and selecting inspect. This will bring us directly to the <mark style="background: #FFB8EBA6;">Elements</mark> tab of the dev tools. Here, we can see the entire layout of the HTML document and all the elements contained inside it. We can even edit the HTML elements in front of us, but be aware that this will only be a local chamge, and will disappear upon reload. 

Also in the Elements tab, we can see the Styles panel. The style panel will display a selected element's CSS properties. *All* of the element's CSS properties will be on display in order of priority. This allows us to see what CSS rules are currently being applied, and which may be being overwritten.

### Console
The <mark style="background: #FFB8EBA6;">Console</mark> tab is a simple REPL environment. Code can be executed here in reference to the webpage, so things like fetch requests *will* work here. Any `console.log()` statements in our code will appear here, so this can be a helpful location to check when debugging.

<mark style="background: #FF5582A6;">REMINDER!</mark> The `console.error(str)` method may do nothing special in VS Code, but in a browser console, it will appear as a giant red block, along with the error location in code. Try to use this specific method when looking to log errors!

On the left of the Console tab, there are sections for messages. Each JS file imported into your code can have their own section for console messages, which can make it easier to debug *what* message is showing up *where*.

### Sources
The <mark style="background: #FFB8EBA6;">Sources</mark> tab allows us to edit page code on the fly. Unlike the console - which is just a simple REPL - we can edit the entirety of a JS or CSS file live. To find the file to edit, look over to the Page tab inside of Sources. There, we will see a file tree of the webpages resources. When we find the file, we can open it right there in the dev tools and begin making changes.

### Network
The <mark style="background: #FFB8EBA6;">Network</mark> tab can be used to view requests and responses made by the webpage. Here is where we will find the headers, method, status codes, and more. 

The body of a request will be found in the payload panel.

### Application
The <mark style="background: #FFB8EBA6;">Application</mark> tab allows us to view and edit the local storage and cookies on a web page. Cookies are essentially short term memory for your browser to store data on the state or status of your page - such as log in information, authorization status, and so on.

## Importing JavaScript Into HTML

### How a page is loaded
A browser is able to display everything in the webpage by following the steps of the <mark style="background: #FFB8EBA6;">Critical Rendering Path</mark> - which is consistent across all browsers.

The first thing a browser does when loading up a webpage is parse the HTML for the creation of the <mark style="background: #FFF3A3A6;">Document Object Model (DOM)</mark>. The HTML is then converted into nodes - which make up the DOM tree. 
*the dom is just the HTML, so it is created once we are done reading the HTML*
<mark style="background: #FFB8EBA6;">REMEBER!</mark> The structure of a webpage is basically a tree. The root node of the tree is the document. From there, the html section is a child node. That is then a parent node to the head and body, and so forth.
![[dom-model.png]]
*The html is the root **element**, but the document is the root **node** of the DOM tree!*

Keep in mind that while the DOM is a tree, we want to think of it as a vertical tree. Since we want to go through a webpage from top to bottom, it will usually be done using a **DEPTH** first search. We dont want to use a **BREADTH** first search because that will cause us to jump around to different sections of the page - starting at the base of the head to the base of the body, back to one layer deeper in the head, and then one layer deeper in the body. A depth first search will reach the leaf of a node before moving on to the next one. In terms of the DOM construction, this means the head and all its contents (meta data, scripts, etc) will be created *before* any body elements.

*If a JS script is encountered during these steps, the DOM creation may be paused so the JS can execute. A large JS file can cause the web page to take a while to load.*


### Importing Scripts
While there are a few bare bones ways we can create interactivity on our pages using only HTML and CSS, we need to use Javascript to get the very most out of our programs. To bring JS into our HTML, all we need is to use a set of `<script></script>` tags. The script tag can have the JavaScript written into the inner HTML, or it can be linked using a `src` attribute and a path (relative or absolute).

```html
<!DOCTYPE html>
<html>
	<head>
		<script>
			//Write JavaScript here
		</script>
	</head>
	<body>
		<script src="./path/code-file.js"></script>
	</body>
</html>
```

As we can see above, we can place a script tag in either the head or body of our html document. While the script can go in either location, good practice will usually be to <mark style="background: #D2B3FFA6;">load the script right before the end of the body</mark>. It is important to note that placement of our scripts *does* in fact matter. 

HTML loads from top to bottom. If we import javascript that tries to access an HTML element that has not yet been loaded in, we can experience an error. For JS dependant on a piece of code, it is better to place the import right before the closing tag of the body. If the JavaScript does not access a specific piece of information, then it can go inside the head of the html.

Top to bottom loading means that the *order* of JavaScript is also just as important. We will encounter errors if one JS file references another file BEFORE it is loaded in.

```html
<!DOCTYPE html>
<html>
	<head>
		<script src=".needsFile2"></script>
		<script src="./file2"></script>
	</head>
	<body>
	</body>
</html>
```

*REMEBER! Code that relies on an external piece of code is called a dependancy*

## The BOM 
The Browser Object Model - or the BOM - is the structure of objects that make up a browser. The Document Object Model - the DOM - is a single part of the BOM. The part of the BOM to get most familair with would be the `window` object, which comes with properties and methods we can use to access and edit pieces of the entire browser window.
 ```js
 window
 //references thwe window object
 
 window.navigator
 //references the navigator object

window.screen
window.screen.height;
window.screen.width;
//references the screen object

window.history
window.history.length;
window.history.back();
//references the history object

window.location
window.location.host;
window.location.pathname;
//gets and sets the location (url) of the window

window.document
window.document.body;
window.document.title;
//references the document (webpage) the browser contains
```

*`window.document` is used so often that we can simply shorted it to `document`*

### Difference between BOM and DOM
The BOM is the browser object model - meaning it is basically the browser. The DOM is the document object model - the collection of html nodes. It is the document the BOM loads in and reads. The DOM is what `window.document` references. 

REMEMBER! The window is the browser equivalent to the global object in Node.js. This means that the `window` object will be the context of any unbound function. 
*Context is the object that the keyword <mark style="background: #ABF7F7A6;">this</mark> refers to in a function*

```js
//try this in the console of a browser
console.log(this);
```

*what will be returned to us in the code above is the window object that stores and manages all the browser's properties and methods.*

## Manipulating DOM Elements
The Browser Object Model - or more specifically the `window` object itself, gives us access to numerous properties and methods that we can use to reference the elements in the document.

REMEMBER! The one we will use most often will be `window.document`, or `document` for short. The document object contains all the html that makes up the DOM - which is a tree structured collection of html element nodes. This includes access to both the body *and* the head of the page.

```js
window.document.title = "New Tab Name";
//the title property lets us change the title dynamically
```

### Grabbing and Finding Elements
Using search methods on the document, there are a number of ways for us to access and modify specific elements. Since the `document` is the root node of the entire DOM, our initial searches will begin here. We can save search time later by assigning the element we found to a variable, and begin searches from that specific node.

*REMEMBER! Each html element in the document is a node in the DOM - each with a collection of their own properties. This means that when we grab an element and save it to a variable, it will be of reference type rather than the entire element itself.*

One of the most basic ways to access a child is refeerencing a parent node's `children`; `element.children` will return an <mark style="background: #FFB8EBA6;">HTMLCollection</mark> *(which is similar to an array)* of its direcrt child nodes. We can then pull the individual element out by referencing the collection's index.

```js
const html = document.children[0]; //[html]
const layer2 = layer1.children; //[head, body]
const body = layer2.children[1];

//Now that we have the body element, we can write into the tags
// ele.innerText will insert text as a string, ignoring tags
body.innerText = "<h1>Written as a string<h1>";

// ele.innerHTML will insert text as HTML code, including tags
body.innerHTML = '<h1>Tags not included</h1>';
```

### Running Script On Page Load
We now know how to import javascript to a webpage. We also know how to use JS to manipulate an element. As a reminder, script tags are usually placed at the end of the body to prevent the JS file from trying to manipulate an element that has not been included in the DOM yet, which would result in an error. 

The DOM only constructs the bare bones HTML elements, and nothing else. What if we wanted to wait for *everything* to finish loading before we run JavaScript? This can include the HTML, CSS, or other media. This is where we would use the `window.onload` property.

We can assign an anoymous function definition to the property, which will be called *only* when the entire page has finished loading in.

```js
window.onload = () => {
	const html = document.children[0]; //[html]
	const layer2 = layer1.children; //[head, body]
	const body = layer2.children[1];
	body.innerHTML = '<h1>A brand new header</h1>';
};
```

## Importing ES6 Modules
REMEMBER! A module is a JS file that exports its contents as an object. In NodeJS, we used the CommonJS import/export syntax.

### CommonJS
CommonJS exporting
```js
const arr = [val, val, val, val];
const func = () => {...};

module.exports = {
	arr, func
}
//we are exporting an object of data
//module.exports is the object
```

CommonJS Importing
```js
const {arr} = require('./file-path');
//destructuring only a single piece of data from the file

const importObj = require('./file-path');
//importObj is now an object that contains all properties and methods

importObj.func();
importObj.arr;
//now the contents can be used in the program
```

### ES6
Here, we will be using the ES6 import/export syntax, which is similar but slightly different. 
*ES6 imports must always be performed at the very top of the file*
ES6 allows us to export pieces of code as we define them with a simple `export` keyword placed in front. This makes exporting multiple items a simple process. Each item exported would have to be imported using the same name.

```js
export class Dog {}

export const arr = [val, val, val];

export function func(){...}
```

To bring the data from the file in as imports, use the import keyword followed by the destructured data name, and then the `from` source as a string.
REMEMBER! When we destructure them, they will be whatever they were in their original file. A class will be a class, a function will be a function. And they must have their original names.

```js
import {Dog} from './file-path';

import {arr} from './file-path';

import {func} from './file-path';

//we could destructure them all at once
import {Dog, arr, func} from '/file-path';
```

If we wanted the import everthing from a file into one single class-like object, we can use the wildcard import to store the properties and methods. This is called an *aliased import*

```js
import * as File from './file-path';
//this can be read as:
//import everything as variable name from the file
//now we can access the properties through dot or bracket notation

new File.Dog();
File.arr;
File.func();
```

An aliased import also allows us to rename the destructured data coming in. It is first destructured by the normal name, followed by an `as` keyword, and finally the new name.

```js
import {origName as newName} from './file-path';
import {origname as newName2} from './file-path';
```

If we wanted to export only a single item, we would use the keywords `export default` before the data. Doing so allows us to directly export a value. This will make it unnamed. We can later assign it any name we'd like when importing.

```js
export default { key: value};
//will be exported without a name

{key2: value2}
//will not be exported at all
```

When importing an unnamed piece of data, we no longer need to destructure the property or method out of the object *since there is no name to destructure*. Instead, we can give the data a name on the fly.

```js
import obj from './file-path';
//obj = { key: value }
```

When using these modules as our JS files in a webpage, we must add one more piece of information to our HTML. The script tag containing an ES6 module must be given an attribute of `type="module"` so the browser knows to read the import/export statements as ES6 modules.

```js
<script type="module" src="./file-path"></script>
```

## Further Selecting DOM Elements
We have seen that DOM elements can be selected by referencing them by their index from the `parent.children` property. While this lets us select a static element, it does not allow us to find and manipulate dynamic elements. To do so, we have access to a few other methods.

```js
const group = document.getElementsByName('tagName');
//scans through the DOM and returns a collection of all matching HTML tags

const group = document.getElementsByClassName('className');
//scans through the DOM and returns a collection of all HTML tags with the specified class

const el = document.getElementById('idName');
//scans through the DOM and returns the only node with a matching ID
```

These are slightly more specific towards the things we are looking for. But we can get even more specific. using the `.querySelector()` method, we can pass in any CSS selectors as a string, and return the first OR a collection of all matching elements. Since we are using css selectors, we can even use CSS combinators.

```js
const el = document.querySelector("tag");
const el = document.querySelector(".class");
const el = document.querySelector("#ID");
//returns the first matching element encountered

const group = document.querySelectorAll("div");
const group = document.querySelectorAll("ul > li");
//returns a collection of all matching elements
```

The `.querySelectorAll()` method is returning a *static NodeList*. 
A *NodeList* is similar to an HTMLCollection - both of which are like an array, and can be converted to one using `Array.from(group)`. 
The *static* means that it is not affected by any new changes made to the DOM after the creation of the NodeList. New elements will not add themselvers to the NodeList.

### Creating New Elements
Creating a single new element is done using the `document.createElement("tag")` method, where we pass in the type of element we want to make. Once we assign the created element to a variable, we can them modify anything necessary on the element. Finally, we can add that element into the document or file using the `node.appendChild(newNode)` method - which will place that node right before the closing tag of the parent.

```js
const newP = document.create('p');
//creates a new paragraph tag

newP.setAttribute("id", "intro");
//setAttribute lets us insert an attribute and its value

const text = newP.createTextNode("Any string");
//createTextNode will take any string as text, good for escaping html characters

newP.appendChild(text);
//adds the new text node to the end of the p tag
//the appendChild method can be called on any node, not just the document

document.body.appendChild(newP);
//adds the new paragraph tag to the end of the body
```

### .innerHTML
Earlier, we saw we can use the `.innerText` property to place a string inside a set of tags. The text passed in will be viewed as literal text. If we were to insert html characters, they would not be interpreted as html.

If we *did* want our text to be interpreted as html, we could use the `.innerHTML` property instead. The benefit to this is being able to update the contents of an element, or to create templates for elements that we can add quickly.

```js
const li = document.createElement("li");
const html = `<figure><img src=${url}><figcaption>${breed}<figcaption></figure>`;
li.innerHTML = html;
const list = document.querySelector("ul");
list.appendChild(li);
```

In the example above, we are able to create several nested elements with dynamic values in only a few lines of code. This could be placed inside a function that will generate many elements for us, rather than coding them individually.

Alternatively, we can also update the text/html inside of an element when something happens. A clock, for example. Here, the innerHTML of the title element will be updated every second to reflect the current time.

```js
setInterval(() => {
	const date = new Date();
	const sec = date.getSeonds();
	const mins = date.getMinutes();
	const hrs = date.getHours();

	const title = document.getElementById("title");
	title.innerHTML = hours + ":" + minutes + ":" + seconds;
}, 1000);
```

## Manipulating Inline CSS
REMEMBER! CSS cascades, meaning that different levels rules will have different priorities. Since only ONE rule can modify a single property, the one with the highest priority will apply. CSS applied directly to an element inside of the html has the highest priority of all. 

While we typically try to avoid inline CSS, we can use JavaScript to make dynamic changes to the appearance of our pages, such as changing the color of text or backgrounds. To do so, all we need to do is access the element's `style` property, followed by the property we want to chage, and a value as a string.

```js
const fruits = document.getElementsByTag("li");
const apple = fruits[0];

apple.style.color = "red";
apple.style.fontSize = "50px";
//The names of the CSS property cannot have dashes, so they are simplified to camelCase
```

## Removing Elements
An element can be completely removed from our html using the `node.remove()` method. That's all!

```js
const firstDiv = document.querySelector("div");
firstDiv.remove();
```

## Event Handling

### Events
An *event* is a state that is triggered by something happening. That can be user interaction or something in the webpage. It is somewhat similar to a psuedo-class, like `:hover` - which responds only when an element is in that state. But instead of CSS, we repond to events with JavaScript.

When an event is triggered, an *event object* is created that stores information and properties about the event. `event.target` is apropertyof the event object that references the node that the event was triggered *on*. Clicking a button will give event.target the reference to that button.

*There are a massive variety of event triggers, including button clicks and cursor movement. There is probably one for any cenario you need, but we will only be focusing on a few common ones for now.*

Some of the more common event include:
- <mark style="background: #FFB8EBA6;">click</mark> - when an element is clicked
- <mark style="background: #FFB8EBA6;">input</mark> - when an input element's value changes: such as *input*, *select*, and *textarea* (these specifically have a property called *contenteditable*) this event triggers on each individual change, including each letter typed in
- <mark style="background: #FFB8EBA6;">change</mark> - similar to input, but triggers only when the element loses *focus* (focus is when an element is selected) such as clicking off the element
- <mark style="background: #FFB8EBA6;">submit</mark> - triggers when a form is submitted - which can be through the form submit button or pressing enter inside the form.
- <mark style="background: #FFB8EBA6;">DOMContentLoaded</mark> - triggered not by user interaction, but instead when the DOM has finished loading all assets. Recall that `window.onload` is the property that waits for ALL assests to load in - html, css, scripts and media. DOMContentLoaded only waits for the DOM elements to complete.

### Event Listeners
Events can be triggered with or without an event listener. Event listeners allow us to execute code when a particular event is triggered. An event listener can be placed on HTML elements AND the window object (for events such as DOMContentLoaded). We then pass in the event and a callback function with the event object parameter to execute when that event is heard.

To add an event listener, all we need to do is call the function on the node we want to listen for an event on with the proper arguments.
`element.addEventListener("event", callback);`
To remove an event listener, all we need to do is call the remove method with the same arguments. It is important to make sure the callback is the same, so the correct event listener can be found in case there are many listeners on a single node.
`element.removeEventListener("event", callback);`

```js
window.addEventListener("DOMContentLoaded", event => {
    const button = document.querySelector("#damage");
    button.addEventListener("click", event => {
        const health = document.querySelector(".health");
        const maxHealth = document.querySelector(".max-health");
        health.style.width = `${((health.offsetWidth - 50)/maxHealth.offsetWidth) * 100}%`;
    });
});
//will decrease the size of a health bar when a button is clicked

window.addEventListener("DOMContentLoaded", event => {
	button.removeEventListener("click", event => {
	    const health = document.querySelector(".health");
	    const maxHealth = document.querySelector(".max-health");
	    health.style.width = `${((health.offsetWidth - 50)/maxHealth.offsetWidth) * 100}%`;
	});
});
```

*the element.offsetWidth property seen above returns the element's width value*

In the example above, notice that we used an event listener on the window, and then chained another event listener inside. This is to ensure that we are loading in all the element before we try to manipulate them. If the content was not fully loaded before we run the script, there would be no button element to retrieve and reference.

Here's an event lsitener making use of the `checked` event.

```html
<!DOCTYPE html>
<html>
  <head>
  </head>
  <body>
    <h1>Pizza Toppings</h1>
    <input type="checkbox" id="on-off">
    <label for="on-off">Extra Cheese</label>
    <div id="now-you-see-me" class="hide">Add $1.00</div>
  </body>
</html>
```

```css
.hide {
    display: none;
}

.show {
    display: block;
}
```

```js
window.addEventListener("DOMContentLoaded", event => {
    const checkBox = document.querySelector("#on-off");
    checkBox.addEventListener("click", event => {
        const div = document.querySelector("#now-you-see-me");
        if(checkBox.checked) {
            div.classList.add("show");
            div.classList.remove("hide");
        } else {
            div.classList.add("hide");
            div.classList.remove("show");
        }
    });
});
```

In this code, we are directly manipulating the classes of an element. Depending on an element's classes, we can give or remove css styles from it. This is the preferred way to modify CSS, since it can be difficult to deeply track where we may be placing inline CSS. 

Note that `classList` works as a collection of all classes. `element.classList.add("class")` and `element.classList.remove("class")` let us modify what classes that element has. If we wanted to set a single class, we could assign a value to the `.className` property.

Lastly, here is an example of the input event at work.

```html
<!DOCTYPE html>
<html>
  <head>
  </head>
  <body>
    <input id="original" type="text" placeholder="Type here">
    <div id="mirrored"></div>
  </body>
</html>
```

```js
window.addEventListener("DOMContentLoaded", event => {
    const mirrorDiv = document.querySelector("#mirrored");
    originalText.addEventListener("input", event => {
		mirrorDiv.innerText = event.target.value;
    });
});
```

*the value property is the value of the input field, meaning its content*
*since the event target in the inner callback is the originalText element (the node with the event listener) we can use event.target to reference it*