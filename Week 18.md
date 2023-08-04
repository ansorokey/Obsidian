## Loading Window vs Loading DOM
As a reminder, the *DOM* is the document object model. This is the tree structured collection of html elements as nodes.
The *BOM* is the browser object model. It is the collection of objects that make up the window and give us interaction. The DOM is one part of the BOM. 

Since there are two differeent models, there are two different functions for when we want to run a script on our page. `window.onload = func` will run javascript once *all resources* inthe page load. This includes the html, css, and javascript.
The event listener `document.addEventListener("DOMContentLoaded", func` will run a script as soon as all the html elements have finished loading. 

The difference between the two is mainly speed and functionality. Loading on the document can save us some resource time, since it will run JS BEFORE large files like images are loaded in. Loading on document can also add in classes and ids to already existing elements that CSS can style once it loads.

Choose the option that is most efficient for your code.

## Event Bubbling
We know that an event is something that is triggered by  a change in sgtate, an action, or condition being met. One of the most common events is a simply click event. This will trigger the event listeners to run the functions given to them. The `event.target` is the element on the page that triggered the event.

*Event bubbling* occurs when a child element triggers the event listener attached to an ancestor element. An event listener attached to an unordered list can be triggered by clickin on its child list item elements. The *bubbling* refers to the nested nature of elements inside one another.
*Rather than bubbles, it may be easiest to think of elements as layers. The innermost layer affects the outer layer it is contained by* 

The event trigger goes through every layer below it. This means parent nodes and their parent's nodes as well. This will go all the way up to the window object.

Below we have an event listener on each element. Clicking on deeper nested elements will trigger the event listeners that belong to ancestor nodes.

```html
<!DOCTYPE html>
<html>
  <head>
    <script>
        window.addEventListener("DOMContentLoaded", event => {
            const body = document.querySelector("#body");
            body.addEventListener("click", event => {
                console.log('body clicked');
            });
            const form = document.querySelector("#form");
            form.addEventListener("click", event => {
                console.log('form clicked');
            });
            const box = document.querySelector("#box");
            box.addEventListener("click", event => {
                console.log('box clicked');
        });
    });
    </script>
    <style>
        body {
            background-color: black;
        }
        form {
            background-color: blue;
            width: 200px;
            height: 200px;
        }
        input {
            background-color: brown;
            width: 50px;
            height: 50px;
        }
    </style>
  </head>
  <body id="body">
    <form id="form">
        <input id="box" type="checkbox">
    </form>
  </body>
</html>
```

While this can be useful in some cases, this can also cause unintended side effects in other cases.

### Preventing Bubbling
In order to prevent event bubbling, we would simply use the `event.stopPropagation();` method inside an event handler. Doing so will keep the event localized to the current element, or to wherever you'd like it to stop. Events will not bubble past this point.

```js
node.addEventListener("click", event => {
	event.stopPropagation();
	console.log('Successfully clicked');
})
```

REMEMBER! 
The `event` object is just a collection of infomation (properties and methods) about the event. 
The element that triggered the event is the `event.target`.
The element that the event listener is attached to is the `event.currentTarget`.
Since a child node can activate a parent node's event listener, the `event.target` can differ from the `event.currentTarget`.

### Event Delegation
Sometimes, we *do* want an event to bubble up to an ancestor. Since any child node can activate the event listener of a parent node, we can simply place the event listener on the parent rather than giving an id and event listener to every single child node - like in an `ul` with many many `li`. In the example below, we place one event listener on the `ul` and have it log the `event.target.` - which is the element that triggers the event. 

```html
<!DOCTYPE html>
<html>
  <head></head>
  <body>
    <ul id="list">
        <li>This is list item 1.</li>
        <li>This is list item 2.</li>
        <li>This is list item 3.</li>
        <li>This is list item 4.</li>
        <li>This is list item 5.</li>
      </ul>
      <script>
        window.addEventListener("DOMContentLoaded", event => {
            const list = document.querySelector("#list");
            list.addEventListener("click", event => {
                console.log(event.target.innerText);
            });
        });
      </script>
  </body>
</html>
```

## Default Behavior and preventDefault
Some html elements have built-in actions that do not need custom defined event listeners to interact with them - such as clicking an anchor tag or checkbox input. These are the default behaviors of the elements. Sometimes, we do not want the user to be able to use these elements until we want them to. We wouldn't want a form submission button to work unless the user entered all valid information.

These default behaviors can be prevented with the built-in event method `event.preventDefault()`.  This method would go inside the event listener of the element we want to stop the default behavior of. Adding a condition can make it so that the element only works if criteria is met. Below, we will stop an anchor tag from working unless a checkbox is checked.

```html
<!DOCTYPE html>
<html>
  <head></head>
  <body>
    <a id="link" href="http://www.google.com">Google</a>
    <br>
    <input id="box" type="checkbox">
    <label for="box">Activate link</label>
      <script>
        window.addEventListener("DOMContentLoaded", event => {
            const link = document.querySelector("#link");
            link.addEventListener("click", event => {
                const box = document.querySelector("#box");
                if(!box.checked) {
                    event.preventDefault();
                }
            });
        });
      </script>
  </body>
</html>
```
*the `.checked` property is a boolean value.
*text input fields also have the `.value` property as strings*

Different elements will have different default behaviors, so look into elements to see what can be prevented.

## "Data-" Attributes in HTML Elements
HTML elements can have any number of attributes set to them. A usefrul one for storing data inside of our html is the "data-" attribute. This allows us to tap into an element's `dataset`, which is similar to a *plain old javascript object*. It's a set of key and value pairs as strings.

### Setting
Placing dataset data into an html element is as simple as using the `element.setAttribute("data-name", strValue)` method. Whatever we want to pass in to the dataset gets appended to "data-", so if we wanted to use a key named "postNumber", we would pass it in like this: `element.setAttribute("data-postNumber", "38751")`

This new attribute will show up in the html of the element, and we can add as many as we'd like.

### Getting
In order to access the data inside an element's dataset, we would reference the specific element's dataset, and then reference the key we want to retrieve the value of.
`element.dataset.key`
The key can be accessed literally, and the returned value will be a string.

### Deleting
Data inside an element's dataset can also be removed. In order to do so, we would simply use the `delete` keyword, followed by the same get technique above.
`delete element.dataset.key`

## Browser Storage
Most of the data we access through the web is stored on remote servers. These servers send data to many users and clients that request it. While they're good for serving a wide audience, they don't particulary excel in serving individual pieces of infomation - that is: specific to single clients. 

Smaller personalized data that doesn't need to be retrieved from a server can be stored locally - on the browser or in the device we are using. Sicne this data is stored, it becomes *stateful*, meaning some data can persist as we travel from page to page, or even when we lose and reopen the browser.
*remember that standard html is stateless, nothing is remembered between responses and requests to server*

Some reasons we would want to use local data storage include:
- Remembering site preferences like color schemes and window zoom
- Saving items inside an online shopping cart
- Saving the information enetered in input fields (if we leave the page)
- Personalized data about the user, like the things they search for and recently viewed
- User log in information/status

### Storage in JavaScript
JavaScript is able to store persistent data in two ways: using *cookies* or the *Web Storage API*. Cookies are small, so they can be communicated to and from servers. Web Storage cannot, but allows us to store more flexible data through `JSON.stringify()`

#### Cookies
Cookies are very small units of memory (less than 4KB) that store key-value pairs of information in a single formatted string. Each key value pair inside the cookies string is ended/separated by a semicolon. To add a cookie, all we have to do is call the `document.cookie` property and assign it to a formatted key-value string. calling `document.cookie` on its own will return the entire cookie.

```js
let newCookie = "username=ansorokey";
document.cookie = newCookie;
newCookie = "password=secret";
document.cookie = newCookie;

document.cookie; //returns 'username=ansorokey; password=secret'
```
The above cookie is one single string containing multiple cookies.

A single cookie can be modified by reassigning the cookie with the same key, but with new values and tags after it.
```js
//creating the first cookie
document.cookie = "monster=DarkMagician";
document.cookie = "type=normal";
document.cookie; // "monster=DarkMagician; type=normal;"

//changing the first cookie
document.cookie = "monster=BlueEyes";
document.cookie; // "monster=BlueEyes; type=normal;"
```

Cookies do not have a specific method to remove or delete cookies. The browser simply removes them from memory when they expire. We can give a cookie an expiration date by including one of two tags after their initial value.
- We can includes a set expiration date using a GMT formatted string (day of week, day month year hr:min:sec GMT)
- We can give the cookie a maximum age in seconds *not milliseconds*

```js
document.cookie = "userName=dragonSlayer; expires=Sat, 8 Feb 1997 00:00:00 GMT";
document.cookie = "pokemon=Pikachu; max-age=0";
```

By default, a cookie only lasts while the page is open. Once you leave the page or close the window/tab, you leave the session - which makes them *session cookies*. 
We can create *persistent cookies* by giving a cookie a specific expiration date.
*there is no distinction in code between the two. The only difference is if an expiration date is given*

Keep in mind that many cookeis can be stored, but `document.cookie` will only return a single string value containuing each and every cookie. If we wanted to find a specific cookie to modify the value, we would need to take the cookie string, split it by `'; '` , spllit each sub value by `'='` and find the matching value at the key. 

#### Web Storage
Web storage is a storage format that is much bigger than cookies *(up to 5mb)*. This data is stored inside the browser's cache - which is like a short term memory. Since it is a larger file size, this data will be stored ONLY on the client side - so no communicating between front-end and back-end.

Data here is contained inside an object with key-value pairs, most similar to a map. We can store any sort of infomation inside them, including other objects and arrays *as long as they are parsed into json with `JSON.stringify(val)`*

This data can last while the browser/tab is open - which is *session storage*, or can remain between sessions when the window is reopened with *local storage*. These are two seperate properties we would call our functions on in code. Both of these properties belong to the window object, and share the same 4 simple functions we need to use them.

```js
//session storage
window.sessionStorage.setItem('key', 'value');
window.sessionStorage.getItem('key'); // returns item
window.sessionStorage.removeItem('key'); // deletes one item
window.sessionStorage.clear(); // deletes all items

//local storage
window.localStorage.setItem('key', 'value');
window.localStorage.getItem('key'); // returns item
window.localStorage.removeItem('key'); // deletes one item
window.localStorage.clear(); // deletes all items
```

Web Storage is very useful for when we want to load in specific resources to a webpage. If they *do* already exist in storage, we can load those values. If they don't, we can create the new values or resources.

Items in storage to not have any expiration dates. They only get removed when specified by the browser, cleared manually, or when the session is left.

## Network Addressing
Being able to manipulate data is easy to do on asingle machine, but sending and recieving that data takes an entire network structure to do so. Where that data goes, and how it knows how to get back to the same device is done through *network addressing*. Several different components from the browser to the machine to the nextwork to the servers make it possible for the request-response cycle to work.

### Ports
A *port* is a virtual connection point for sotfware. With all the data a device recieves, a piece of software - such as the browser - needs to know where to find the data it requested. Ports can best be throught of as mailboxes. A letter can come into the mailroom (our device) and be addressed to a specific location in the mail room. That way, only the data meant for the browser goes to the browser. Data going to other software goes to their respective ports. 

The two main data transmission protocols used for computer networking are TCP and UDP. As such, each protocol has **65536** ports available to them from **0 - 65535**. Thats 65000 tcp ports and 65000 upd ports. `TCP 25` and `UDP 25` are two different ports unrelated to each other.

With over 130 thousand possioble ports available, it would be chaos if every device has a unique location for incoming software data. As such, certain ports and ranges of ports are dedicated for commonly used applications and purposes. This means that a browser on one computer will know to look for data in the same port it would look on another. 

The three port ranges are:
- 0 - 1023: *System Ports*
	Standardized in the early days of the internet, and reserved for the most well known services, including `80` for HTTP and `25` for e-mail.

- 1024 - 49151: *User Ports*
	Formerly known as *registered ports*, this is where most common or custom applications and software will establish a port. Useful for nearly ANY applciation, and where the ports used for development are located: `3000`, `5000`, `8080`

- 49152 - 65535: *Dynamic ports*
	These are *ephemeral* ports - meaning they are short term, temporary ports. These are usually used for server connections or for building web sockets. Not recommended for custom application develeopment, but possible.

### Mac Addresses
A *Media Access Control* address - MAC Address - is a physical identifier given to a device's hardware when it is made. Unlike an IP address, these *cannot* be changed. Think of it as an etched in serial number for any device that can be read by software. For this reason, they can also be referred to as a *physical address*. These are physically permenant, however an operating system *can* spoof the MAC address it reports. This can cause safety concernsif a network or application relies only on MAC addresses.

A MAC address is written in SIX double-digit hexidecimals. Dashes can be included or left out, and letters can be uppercase or lowercase. They will be all understood by the network.
`A1-B2-C3-D4-F6` or `H321BBC246AA`

These MAC addresses are used to identify starting and end points in the network, so data knows which device data came from and where to send it back. The information about a MAC address is stored within the *layer's frame* as a source address and destination address - along with data such as the transport protocol wrapper and IP wrapper This is used in wi-fi, bluetooth, and standard wired internet devices. 
*A frame is the initial information sent from the client that gets wrapped or unwrapped with each layer it travels through. Each layer in the network adds information like headers and where to go next, and wrapped around the entire frame for the next layer*
```
. //initial data
(.) //given first layer wrapper
{(.)} //another layer wrapped
[{(.)}] //another layer wrapped
{(.)} //unpacked layer
(.) //unpacked layer
. //data recieved
```

### DNS
A DNS - or *Domain Name System* - is a key part of the network. All servers and clients exist on the internet as IP addresses. That is, they are all just numbers in cyberspace. Every website has an IP address that must be accessed to view the page. We as humans do not type in the exact address for websites. Instead, we use a website's name - its*domain*. 

*A DNS is a Domain Name System - not a server. Many organizations and networks contribute information to servers full of addresses. It is common for one DNS server to not have a site's address at all, and request information from a different DNS server that may. These servers together make up the system component*

The DNS is the intermediary step between humans typing in a website name and beinbg connected to that website's IP address. It is essentially a phone book for the internet. It looks up a domain name, and returns the address.

### Domains
A website's name is its domain. A domain is a part of the url, but the entire url is not the domain, since the url includes query information and the resource identifiers. The domain is the name that the DNS looks up to provide an IP address.

The dns is split into several different parts:
![[domain-name.png]]

- *Top Level Domain*
	The last segment of the domain before the restful routes and query begin. The most common examples include `.com`, `.org`, and `.net`. These top level domains are managed by giant internet organizations. The sites that belong to these top level domains are part of that *domain registry*. These registries can be organization owned, government owned, or privately owned.

- *Second Level Domain*
	The portion that comes right before the top level domain, generally the recognized name of the site. The second level domain and the top level domain are usually combined and referred to the domain as a whole. These second level domains are also managed by the same domain registries, and can be purchased fom them.

Sites can have additional subdomains (that increas in level - third, fourth, etc). These can be free and made by the consumer. 

### DNS and Domain Process
A site's IP address is found using these domains and the DNS server. The web browser makes a request to a *DNS Root Name Server* which holds a collection of the top level domains. This server looks up the provided top level domain, and then directs the client to that server.
When that top level domain DNS server is reached, it then looks up the second level domain within its directories, and finds the appropriate server that holds that information. Another redirect occurs to the subdomain. When that server is reached, it should be able to find the correct IP address, and provide it back to the client.

### DNS Records
Each DNS server contains a text file called a *zone file* - this is where the information such as host names, IP addresses, and resources types are held. Below is an example.

```
$TTL 299
my-site.com.    IN  SOA     ns1.cloudflare.com. dns.cloudflare.com. 2032032092 10000 2400 604800 3600
my-site.com.    IN  NS      ns1.my-site.com.
my-site.com.    IN  NS      ns2.my-site.com.
my-site.com.    IN  A       104.28.31.159
my-site.com.    IN  A       104.28.30.159
my-site.com.    IN  AAAA    2606:4700:30::681c:1f9f
my-site.com.    IN  AAAA    2606:4700:30::681c:1e9f
www             IN  CNAME   my-site.com.
ns1             IN  A       104.28.31.150
ns2             IN  A       104.28.30.150
my-site.com.    IN  MX      10 mail.google.com.
```

The terms above include: 
- SOA
	The *Start OF Authority* is a record that identifies the *master* or *primary* authority, which is the main name server for the domain being searched. *This is required in every zone file, since it is the default returned if nothing else is found*

- NS
	NS records point to name servers for the zone. There are usually at least two per zone. Since DNS is a system and not a single server, we can point to several name servers for a single domain. This can be used for picking the fastest and closest server, or in case one server goes down, others can still be used and the site can be accessed.

- A / AAAA
	These records provide the direct path to an IP address. They are the key component to DNS servers.
	A records are used for IPv4, and AAAA records are used for IPv6.

- CNAME
	An alternative name for a domain that points to the same server/address as another name.

- MX
	*Mail Exchanger records* are used by email clients to direct messages to the appropriate mail servers, since the end of any email is ALSO a domain name (gmail.com)

- $TTL
	This is the *time to live* for record. When we make DNS requests, the network undergoes an entire lengthy process to find and return the IP Address we supplied. Rather than doing that same process over and over every timne we want to access that address, our devices *cache* the record in memory. This way, we can reach the same IP address much quicker and with much less effort and network traffic. 
	The time to live determines how long the record should be cached in seconds. This does not account for changes in location, however. So a shorter TTL can accomodate an IP address that jumps around servers, while a longer TTL is better for a stable server that doesn't change location often.
