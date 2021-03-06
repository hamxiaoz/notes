# DOM

* DOM = `window.document`
* BOM = `window.document, window.navigator, window.location, window.history, window.screen, etc`
* [JQuery method with native js](http://youmightnotneedjquery.com/)
* in DevTool, `$0` returns the selected Element, so you can use normal DOM APIs to access: ex, `$0.classList`

## DOM Ready

* event `DOMContentLoaded` : the browser fully loaded HTML, and the DOM tree is built, but external resources like pictures `<img>` and stylesheets may be not yet loaded.
  * `document.addEventListener("DOMContentLoaded", ready);`
  * the `defer` scripts already loaded
  * the `async` scripts might NOT be loaded
* function `load`: the browser loaded all resources \(images, styles etc\)
  * `window.onload = function() {}` 
* function `beforeunload/unload` : when the user is leaving the page
  * `window.unload = function() {}`

```javascript
// handle the case when we add the callback after the document is already loaded 
if (document.readyState === "complete" ||
    (document.readyState !== "loading" && !document.documentElement.doScroll)
) {
  callback();
} else {
  document.addEventListener("DOMContentLoaded", callback);
}
```

Reference: [https://javascript.info/onload-ondomcontentloaded](https://javascript.info/onload-ondomcontentloaded)

Script: async vs defer

* Normally, if browser see `<script>` in `<head>`, it will stop parsing until downloaded
* script in bottom of the body will not block. But its own problem is, the script won't be downloaded until the full page is parsed.
* `defer`: async downloading scripts \(download in order\), execute after parsing HTML is done
* `async`: async downloading scripts \(downloading might be out of order\), execute after downloading is done \(will pause parsing HTML\)
* reference:
  * [http://www.growingwiththeweb.com/2014/02/async-vs-defer-attributes.html](http://www.growingwiththeweb.com/2014/02/async-vs-defer-attributes.html)
  * [https://stackoverflow.com/questions/436411/where-should-i-put-script-tags-in-html-markup](https://stackoverflow.com/questions/436411/where-should-i-put-script-tags-in-html-markup)

## DOM Manipulation

Node \(like root interface\) -&gt; Document -&gt; Element

### Selector

You can use the following selector on `document` or `element`.

NOTE you cannot use pure number in your selector

* The selector attributes should be CSS identifiers or strings. So you cannot use number there!
* `var a = document.querySelector('a[data-a="1"]');`

Select the first or null \(`$(selector)` in DevTools\):

* `const myElement = document.querySelector('#foo > div.bar input[name="login"]')`
* Returns the first descendant [`Element`](https://developer.mozilla.org/en-US/docs/Web/API/element) using DFS + Pre-order traversal
* It's not live, compare to `getElementsByTagName()`

Select all matching \(`$$(selector)` in DevTools\):

* `const myElements = document.querySelectorAll('.bar')`
* Returns [`NodeList`](https://developer.mozilla.org/en-US/docs/Web/API/NodeList)
* iterate: `for .. of` or `forEach` or `.item(i)`

Compare/Match:

* `node.contains(element)`
* `e.target.nodeName == "LI"`
* `e.target.matches('input')` \(Element.matches\)

Traverse:

* traverse parents: `element.closest('selector')`
* how to polyfill? see [MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/closest)

Others: using above is recommended.

```javascript
  const items = document.getElementsByClassName('item');
  const item = document.getElementById('item');
```

### Attributes

Generally, you can do:

* `element.getAttribute('id')` or `element.getAttribute('data-parent')`
* `element.setAttribute('tabindex', 3);`
* `element.id`

[Data attributes](https://developer.mozilla.org/en-US/docs/Learn/HTML/Howto/Use_data_attributes)

* js: it's accessible \(read/write\) from js by the `dataset` property. Note dashes are converted to camelCase\)
* css: `attr()`

```javascript
// <article
//   id="electriccars"
//   data-columns="3"
//   data-index-number="12314"
//   data-parent="cars">
// ...
// </article>
var article = document.getElementById('electriccars');
article.dataset.columns // "3"
article.dataset.indexNumber // "12314"
article.dataset.parent = 'makes'; // "cars" -> 'makes'

// css
// article::before {
//   content: attr(data-parent);
// }
```

### CSS

On make item invisible as well as clickable: [https://css-tricks.com/snippets/css/toggle-visibility-when-hiding-elements/](https://css-tricks.com/snippets/css/toggle-visibility-when-hiding-elements/)

```javascript
// CRUD class
myElement.classList.add('foo', 'bar')
myElement.classList.remove('bar')
myElement.classList.toggle('baz')
myElement.classList.replace('bar', 'baz')

// fetch class
myElement.className // -> 'foo bar'
myElement.classList.contains('foo') // -> 'foo bar'

// show or hide button
stopBtn.style.display = 'none'; // '';

// set style (this won't get all styles)
myElement.style.marginLeft = '2em'

// get all styles as text
var text = myElement.style.cssText; // "color: red;"

// get all styles
window.getComputedStyle(myElement).getPropertyValue('margin-left')

// Animation
const start = window.performance.now()
const duration = 2000

window.requestAnimationFrame(function fadeIn (now)) {
  const progress = now - start
  myElement.style.opacity = progress / duration

  if (progress < duration) {
    window.requestAnimationFrame(fadeIn)
  }
}
```

### Element CRUD

```javascript
// create
const myNewElement = document.createElement('a');
myNewElement.setAttribute('href', 'http://zurassic.com/');

// append as child
element1.appendChild(element2)
// this can also move existing element
element1.appendChild(document.querySelecotr('.existing'));

// insert into precise location, can be same level or as children
// [insertAdjacentHTML](https://developer.mozilla.org/en-US/docs/Web/API/Element/insertAdjacentHTML)
document.querySelector("body").insertAdjacentHTML("beforeend", '<div class="loader"></div>');
// this can also move existing element
document.querySelector("body").insertAdjacentHTML("beforeend", document.querySelecotr('.existing'));

// remove
myElement.parentNode.removeChild(myElement)

// clone
const myElementClone = myElement.cloneNode()

// preferment version using [DocumentFragment](https://developer.mozilla.org/en-US/docs/Web/API/DocumentFragment)
// It's minimal DOM, similar to StringBuilder
const fragment = document.createDocumentFragment()
fragment.appendChild(text)
fragment.appendChild(hr)

myElement.appendChild(fragment)
```

### Update content

```javascript
myElement.innerHTML = '<div>foo</div>'
myElement.textContent = 'abc'
```

### Observe Changes

The `MutationObserver` interface provides the ability to watch for changes being made to the DOM tree.

You can observe changes \(child, attributes, style, etc\) and pause in dev tools:

```javascript
function observe(el) {
  var MutationObserver = window.WebKitMutationObserver;

  var observer = new MutationObserver(function(mutations) {
    mutations.forEach(function(mutation) {
      console.log(
        "old",
        mutation.oldValue,
        "new",
        mutation.target.style.cssText,
        "mutation",
        mutation
      );
      if (mutation.attributeName == property) debugger;
    });
  });

  var config = {
    attributes: true,
    attributeFilter: ["class"], // only observe class changes
    attributeOldValue: true
  };

  observer.observe(el, config);
}

observe(document.querySelector(".my-test"));
```

### Common Functions

```javascript
// Rename a tag (change tag type). Ex, <div> -> <a>
function renameTag(target, newNode) {
  // copy attributes
  for (var i = 0; i < target.attributes.length; i++) {
    newNode.setAttribute(
      target.attributes[i].nodeName,
      target.attributes[i].nodeValue
    );
  }

  // move children. firstChild is a live API so we can 'while' on that
  while (target.firstChild) {
    newNode.appendChild(target.firstChild);
  }

  // copy in-line styles
  if (target.style.length > 0) {
    newNode.style.cssText = target.style.cssText;
  }

  return target.parentNode.replaceChild(newNode, target);
}
```

## BOM

* Reload window: `location.reload();`
* Scroll to top: `window.scrollTo(0, 0);`

## [Event](https://developer.mozilla.org/en-US/docs/Web/API/Event)

Don't use `onclick`: single property, essentially override it.

### Event CRUD

```javascript
// add
myElement.addEventListener('click', (event)=> {
  console.log(event.type + ' got fired')
})

// remove
myElement.addEventListener('change', function listener (event) {
  console.log(event.type + ' got triggered on ' + this)
  this.removeEventListener('change', listener)
})

// match all children (live too)
myForm.addEventListener('change', function (event) {
  const target = event.target
  if (target.matches('input')) {
    console.log(target.value)
  }
})
```

### Override Default Event

* `preventDefault()` stop default handling, it'll still propagate
* `stopPropagation()` stop bubbling up
* `stopImmediatePropagation()` stop handling in current layer, stop passing to other event handlers: If several listeners are attached to the same element for the same event type, they are called in order in which they have been added. If during one such call, event.stopImmediatePropagation\(\) is called, no remaining listeners will be called.

### e.target vs e.currentTarget:

* `e.target` identifies the element on which the event occurred
* `e.currentTarget` always refer to the the element to which the event handler has been attached

### `this`

* usually points to the DOM element where the handler is bound.
* NOTE if you add event using **arrow function** then it's `document` instead of the element!

### How to trigger event manually:

[https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating\_and\_triggering\_events](https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Creating_and_triggering_events)

How to perform click? `element.click()`

### Keyboard/Mouse events:

* See examples from: [https://eloquentjavascript.net/15\_event.html](https://eloquentjavascript.net/15_event.html)

### Custom events

interface for any custom event:

* CustomEvent: [https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent](https://developer.mozilla.org/en-US/docs/Web/API/CustomEvent)
* custom implementation of EventEmitter

### Event Delegation

Use one event handler to handle all children events

```markup
  <div id="menu">
    <button data-action="save">Save</button>
    <button data-action="load">Load</button>
    <button data-action="search">Search</button>
  </div>

  <script>
    class Menu {
      constructor(elem) {
        this._elem = elem;
        elem.addEventListener('click', this.onClick.bind(this)); // (*)
      }

      save() {
        alert('saving');
      }

      load() {
        alert('loading');
      }

      search() {
        alert('searching');
      }

      onClick(event) {
        let action = event.target.dataset.action;
        if (action) {
          this[action]();
        }
      };
    }

    new Menu(menu);
  </script>
```

Event delegation can also be used to add behavior to any element \(with data-attr\):

```javascript
  // use with <button data-counter>add</button>
  document.addEventListener('click', function(event) {
    if (event.target.dataset.counter != undefined) {
      // if the attribute exists...
      event.target.value++;
    }
  });
```

