
## What is the DOM?

**DOM (Document Object Model)** is a **tree-like structure** that represents the **content and structure** of your HTML document inside the browser.

When a browser loads an HTML file, it reads (or _parses_) the text and converts it into a **hierarchical object model** — the **DOM Tree**.

###  Example HTML

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Demo Page</title>
  </head>
  <body>
    <h1>Hello World!</h1>
    <p>Welcome to the DOM example.</p>
  </body>
</html>
```

### DOM Tree Representation

```
Document
 └── html
      ├── head
      │    └── title → "Demo Page"
      └── body
           ├── h1 → "Hello World!"
           └── p → "Welcome to the DOM example."
```

Each part of  HTML becomes a **node** in the DOM tree:

- **Document Node:** The root of everything.
    
- **Element Nodes:** HTML tags (`<body>`, `<p>`, etc.)
    
- **Text Nodes:** Text inside tags.
    
- **Attribute Nodes:** Attributes like `class`, `id`, `href`.
    

---

##  Why the DOM Matters (What is the importance or role of the DOM in web development?)

The DOM lets **JavaScript interact with HTML** dynamically.

### 💡 Examples

```js
// Change text
const heading = **document.querySelector('h1');**
heading.textContent = 'DOM Manipulated!';

// Change style
heading.style.color = 'blue';

// Create new element
a = document.createElement('a');
a.textContent = 'Visit Site';
a.href = 'https://example.com';
document.body.appendChild(a);
```

Everything we modify with JavaScript — from text to CSS classes — happens through the DOM.

---

## DOM Construction Steps

When a browser renders a webpage, it follows these steps:

1. **HTML Parsing** – Browser reads raw HTML text.
    
2. **Tokenization** – Breaks into tags, attributes, text.
    
3. **DOM Tree Creation** – Converts them into structured nodes.
    
4. **CSSOM Tree Creation** – Builds CSS object model (explained next).
    
5. **Render Tree Construction** – Combines DOM + CSSOM.
    
6. **Layout** – Calculates position and size of elements.
    
7. **Painting** – Draws pixels on the screen.
    

---
# HTML Tree Construction (How Tokens Become the DOM)

After **tokenization**, browsers need to take the emitted tokens and **build the actual structure of the web page** — the **DOM Tree** (Document Object Model).

This process is called **Tree Construction** and is handled by the **Tree Builder** within the browser’s HTML parser.

---

##  Overview of the Tree Construction Phase

Once tokens (like start tags, end tags, text, etc.) are emitted from the **HTML Tokenizer**, they’re passed to the **Tree Construction stage**. This stage determines:

- **What kind of node** to create (Element, Text, Comment, etc.)
    
- **Where** in the tree to place it (its parent/child relationship)
    
- **How** to handle nesting, errors, and missing tags.
    

---

## The Core Mechanism

Browsers maintain a few internal structures during this process:

### a. Document Object Model (DOM)

This is the output tree being built.

### b. Stack of Open Elements

- A **stack data structure** where the browser keeps track of currently open elements.
    
- Each time a start tag (`<div>`) is seen, it’s **pushed** to the stack.
    
- When the corresponding end tag (`</div>`) is seen, it’s **popped** from the stack.
    

This helps the browser know where to attach child nodes.

###  c. Insertion Modes

The parser changes its behavior depending on what part of the document it’s currently parsing:

| Insertion Mode       | Description                                       |
| -------------------- | ------------------------------------------------- |
| **Initial**          | Before anything else; waiting for DOCTYPE.        |
| **Before HTML**      | Looking for `<html>` root.                        |
| **Before Head**      | Waiting for `<head>`.                             |
| **In Head**          | Inside `<head>`.                                  |
| **After Head**       | Just finished `<head>`, now waiting for `<body>`. |
| **In Body**          | Inside the `<body>` content.                      |
| **After Body**       | After closing the `<body>` tag.                   |
| **After After Body** | After the entire document has ended.              |

---

##  Step-by-Step Example

Let’s parse the following HTML:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>Tree Example</title>
  </head>
  <body>
    <h1>Hello</h1>
    <p>World</p>
  </body>
</html>
```

### Step 1: Start — Initial Mode

- Tokenizer emits: `<!DOCTYPE html>`
    
- Parser recognizes DOCTYPE → switches to **Before HTML Mode**
    

### Step 2: `<html lang="en">`

- Create `HTMLHtmlElement` node.
    
- Push `<html>` onto the **Stack of Open Elements**.
    

**Stack:** `[ html ]`

### Step 3: `<head>`

- Create a new `<head>` node.
    
- Append as child of `<html>`.
    
- Push `<head>` to stack.
    

**Stack:** `[ html, head ]`

### Step 4: `<title>`

- Create `<title>` node → append to `<head>`.
    
- Push `<title>`.
    

**Stack:** `[ html, head, title ]`

### Step 5: Text `Tree Example`

- Create Text Node inside `<title>`.
    

### Step 6: `</title>`

- Pop `<title>` from stack.
    

**Stack:** `[ html, head ]`

### Step 7: `</head>`

- Pop `<head>`.
    
- Switch to **After Head Mode**.
    

**Stack:** `[ html ]`

### Step 8: `<body>`

- Create `<body>` node → append to `<html>`.
    
- Push `<body>`.
    
- Switch to **In Body Mode**.
    

**Stack:** `[ html, body ]`

### Step 9: `<h1>` → Text → `</h1>`

- Create `<h1>` → append to `<body>`.
    
- Push `<h1>` → then text → then pop `<h1>`.
    

**Stack:** `[ html, body ]`

### Step 10: `<p>` → Text → `</p>`

- Create `<p>` → append to `<body>`.
    
- Push `<p>` → then text → then pop `<p>`.
    

### Step 11: `</body>` → `</html>`

- Pop remaining elements → DOM complete.
    

**Final Stack:** `[]`

---

##  4. Visual Representation

```
<!DOCTYPE html>
│
└── html (lang="en")
    ├── head
    │   └── title → "Tree Example"
    └── body
        ├── h1 → "Hello"
        └── p → "World"
```

---

## 5. How Browser Handles Malformed(structured incorrect) HTML

HTML parsers are **error-tolerant**. If a developer forgets a closing tag, the parser automatically infers structure using the **Stack of Open Elements**.

Example:

```html
<body>
  <p>Hello
  <div>World</div>
</body>
```

Even though `<p>` wasn’t closed, the browser auto-corrects:

```
<body>
  <p>Hello</p>
  <div>World</div>
</body>
```

This behavior is part of the _HTML5 parsing algorithm_ specification.

---

## 6. Tree Construction and the DOM API

Once the DOM Tree is built:

- JavaScript can access it using the **DOM API** (`document.querySelector`, `getElementById`, etc.).
    
- CSS uses the **CSSOM** to style these nodes.
    
- The **Render Tree** is created by combining DOM + CSSOM.
    

```text
HTML → Tokenizer → Tokens → Tree Builder → DOM → (with CSSOM) → Render Tree → Layout → Paint
```

---

##  Example — Visual Timeline of Building

```text
1️⃣ Tokenizer emits <html> → DOM creates <html>
2️⃣ Tokenizer emits <body> → Append inside <html>
3️⃣ Tokenizer emits <h1> → Append inside <body>
4️⃣ Tokenizer emits Text("Hello") → Child of <h1>
5️⃣ Tokenizer emits </h1> → Close h1
6️⃣ Tokenizer emits <p> → Append inside <body>
7️⃣ Tokenizer emits Text("World") → Child of <p>
8️⃣ Tokenizer emits </p> → Close p
```

Result:

```html
<html>
  <body>
    <h1>Hello</h1>
    <p>World</p>
  </body>
</html>
```

---

##  Key Takeaways

- **Tokenization** creates structured tokens.
    
- **Tree Construction** consumes tokens to form the DOM.
    
- **Stack of Open Elements** ensures proper nesting.
    
- **Insertion Modes** control context-specific behavior.
    
- **Browsers auto-correct malformed HTML** to ensure pages still render.
    

---

# Understanding DOM Nodes

---

##  What is a DOM Node?

A **node** is a **single point** in the **Document Object Model (DOM) tree**. Every part of an HTML document — elements, text, attributes, comments — is represented as a **node**.

Think of the DOM as a **family tree**, and each tag, text, or attribute as a **member (node)** in that tree.

---

## Types of DOM Nodes

|Node Type|Description|Example|
|---|---|---|
|**Document Node**|The root of the DOM tree.|`document`|
|**Element Node**|Represents an HTML tag.|`<div>`, `<p>`, `<h1>`|
|**Text Node**|Represents the text inside elements.|`"Hello World"`|
|**Attribute Node**|Represents element attributes.|`id="title"`|
|**Comment Node**|Represents HTML comments.|`<!-- This is a comment -->`|

### Example:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <title>DOM Node Example</title>
  </head>
  <body>
    <h1 id="title">Hello World!</h1>
    <!-- This is a comment -->
  </body>
</html>
```

### DOM Tree Representation

```
Document
 └── html (Element Node)
      ├── head (Element Node)
      │    └── title (Element Node)
      │         └── "DOM Node Example" (Text Node)
      └── body (Element Node)
           ├── h1 (Element Node, attribute: id="title")
           │    └── "Hello World!" (Text Node)
           └── Comment Node → <!-- This is a comment -->
```

---

## Accessing Nodes in JavaScript

You can use **DOM APIs** to access or manipulate these nodes.

### Example:

```html
<h1 id="heading">Welcome</h1>
<p>This is a paragraph.</p>

<script>
  // Access element node
  const heading = document.getElementById('heading');
  console.log(heading.nodeName); // Output: H1

  // Access text node inside element
  console.log(heading.firstChild.nodeType); // Output: 3 (Text Node)
  console.log(heading.firstChild.nodeValue); // Output: Welcome

  // Create new element node
  const newPara = document.createElement('p');
  const textNode = document.createTextNode('This is added dynamically.');
  newPara.appendChild(textNode);
  document.body.appendChild(newPara);
</script>
```

---

## `nodeType` Values

| Node Type | Constant              | Numeric Value |
| --------- | --------------------- | ------------- |
| Element   | `Node.ELEMENT_NODE`   | `1`           |
| Attribute | `Node.ATTRIBUTE_NODE` | `2`           |
| Text      | `Node.TEXT_NODE`      | `3`           |
| Comment   | `Node.COMMENT_NODE`   | `8`           |
| Document  | `Node.DOCUMENT_NODE`  | `9`           |

You can use `node.nodeType` in JS to check what kind of node you are working with.

```js
console.log(document.nodeType); // 9
console.log(document.body.nodeType); // 1
console.log(document.body.firstChild.nodeType); // 3
```

---

## Node Properties

|Property|Description|Example|
|---|---|---|
|`.nodeName`|Name of the node (`DIV`, `#text`, etc.)|`element.nodeName`|
|`.nodeType`|Type of node (number)|`element.nodeType`|
|`.nodeValue`|Text content of node|`textNode.nodeValue`|
|`.childNodes`|Returns NodeList of children|`element.childNodes`|
|`.parentNode`|Returns parent node|`child.parentNode`|
|`.firstChild`, `.lastChild`|Access first/last child node|`element.firstChild`|
|`.nextSibling`, `.previousSibling`|Navigate between sibling nodes|`element.nextSibling`|

---

## Node Relationships

Nodes have relationships — **parent**, **child**, and **sibling**.

### Example:

```html
<div id="parent">
  <p>First paragraph</p>
  <p>Second paragraph</p>
</div>
```

### Relationship Diagram

```
Parent Node: <div>
 ├── Child Node 1: <p>First paragraph</p>
 └── Child Node 2: <p>Second paragraph</p>
```

### JS Example

```js
const parent = document.getElementById('parent');
const firstChild = parent.firstElementChild;
const next = firstChild.nextElementSibling;

console.log(parent.childNodes.length); // Counts all child nodes (including text)
console.log(firstChild.textContent); // "First paragraph"
console.log(next.textContent); // "Second paragraph"
```

---

## Difference Between `childNodes` and `children`

|Property|Includes|Excludes|
|---|---|---|
|`.childNodes`|Elements, text, comments|None|
|`.children`|Only element nodes|Text/comments|

```js
console.log(parent.childNodes); // NodeList (includes text)
console.log(parent.children);   // HTMLCollection (only elements)
```

---

## Creating and Removing Nodes

```js
// Create element node
const div = document.createElement('div');

// Create text node
const text = document.createTextNode('Hello DOM!');

// Append text to element
div.appendChild(text);

// Add to body
document.body.appendChild(div);

// Remove node
document.body.removeChild(div);
```

---

##  Practical Example

```html
<!DOCTYPE html>
<html>
  <body>
    <ul id="fruits">
      <li>Apple</li>
      <li>Banana</li>
    </ul>

    <script>
      const list = document.getElementById('fruits');

      // Create a new list item
      const newItem = document.createElement('li');
      newItem.textContent = 'Mango';

      // Append new node to list
      list.appendChild(newItem);

      console.log(list.childNodes); // includes text + li
      console.log(list.children);   // only li elements
    </script>
  </body>
</html>
```

---

## Summary

- Every part of an HTML page is represented as a **node** in the DOM.
    
- Types: Document, Element, Text, Attribute, Comment.
    
- Nodes form a **hierarchical tree structure**.
    
- JS uses DOM APIs to **create, edit, and delete** nodes dynamically.
    
- Knowing node relationships is essential for DOM manipulation.
    


## 4. CSSOM (CSS Object Model)

While DOM represents **HTML structure**, **CSSOM** represents **CSS styles**.

When CSS files (or `<style>` tags) are loaded, browsers parse them into another tree called the **CSSOM**.

### Example CSS

```css
body {
  font-family: Arial;
  background-color: #fafafa;
}

h1 {
  color: blue;
  font-size: 24px;
}
```

### CSSOM Tree

```
CSSStyleSheet
 ├── body
 │    ├── font-family: Arial
 │    └── background-color: #fafafa
 └── h1
      ├── color: blue
      └── font-size: 24px
```

The browser then **combines** the **DOM Tree** and **CSSOM Tree** to create the **Render Tree**, which determines _how_ the page visually looks.

---

## 5. DOM + CSSOM → Render Tree

| Step         | Output                        |
| ------------ | ----------------------------- |
| HTML Parsing | DOM Tree                      |
| CSS Parsing  | CSSOM Tree                    |
| Merge        | Render Tree                   |
| Layout       | Position and size of elements |
| Paint        | Draws pixels                  |

### Visualization

```
HTML → DOM Tree
CSS  → CSSOM Tree
DOM + CSSOM → Render Tree → Layout → Paint → Screen
```

---

## 6. Key Differences Between DOM & CSSOM

|Feature|DOM|CSSOM|
|---|---|---|
|Represents|HTML content|CSS styles|
|Built from|HTML parser|CSS parser|
|Modifiable via|`document` object|`document.styleSheets`|
|Affects|Structure & content|Presentation|
|Updates trigger|Repaint / Reflow|Repaint / Reflow|

---

## 7. Performance Tip

- Changing the **DOM structure** (adding/removing elements) causes **reflow** (layout recalculation).
    
- Changing **CSSOM** (adding/removing styles) may cause **repaint** (visual update).
    
- To optimize:
    
    - Batch DOM updates (e.g., use `DocumentFragment`).
        
    - Avoid forced reflows.
        
    - Use CSS transitions/animations instead of JS-heavy effects.
        

---

## 8. Practical Example

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>DOM & CSSOM Example</title>
    <style>
      h1 { color: green; }
    </style>
  </head>
  <body>
    <h1 id="title">Welcome!</h1>

    <script>
      // Access the DOM
      const heading = document.getElementById('title');
      heading.textContent = 'Updated by JavaScript';
      heading.style.color = 'crimson'; // Accesses CSSOM to update style
    </script>
  </body>
</html>
```

Here:

- Browser parses HTML → builds **DOM Tree**.
    
- Browser parses CSS → builds **CSSOM Tree**.
    
- JS changes both DOM (text) and CSSOM (style).
    

---

## 9. DOM API Essentials

|Action|JS Method|
|---|---|
|Select element|`document.querySelector()`|
|Select multiple|`document.querySelectorAll()`|
|Create element|`document.createElement()`|
|Add element|`appendChild()` / `insertBefore()`|
|Modify text|`textContent`, `innerHTML`|
|Change attributes|`setAttribute()`, `getAttribute()`|
|Modify styles|`.style.property`|

---

## 10. Summary

- **DOM** = HTML structure in memory.
    
- **CSSOM** = CSS styling in memory.
    
- **Render Tree** = Merged visual representation.
    
- **JavaScript** uses DOM APIs to manipulate both.
    
- Efficient updates avoid unnecessary reflows and repaints.
    

---

# Render Tree Construction (DOM + CSSOM → Render Tree)

After the **DOM** (structure) and **CSSOM** (styles) are constructed, the browser merges them to create a **Render Tree** — a visual representation of what will actually appear on the screen.

---

## Step 1: DOM + CSSOM Merge → Render Tree

- **DOM Tree**: Represents the document’s structure (elements, attributes, text nodes).
    
- **CSSOM Tree**: Represents the styling information (selectors, rules, computed styles).
    

The **Render Tree** is built by walking the DOM and finding the matching computed styles for each **visible** element.

```plaintext
DOM Tree              CSSOM Tree               Render Tree
----------             ----------              ------------
<html>                 html { display: block; }   RenderRoot
  <body>               body { margin: 8px; }      ├── Body (block)
    <h1>               h1 { font-size: 2em; }     │   ├── H1 (inline)
      "Hello"                                    │       └── Text: "Hello"
    <p>                 p { color: blue; }        └── P (block)
      "World"                                    └── Text: "World"
```

**Note:** Elements like `<head>`, `<meta>`, or elements with `display: none` are **not included** in the Render Tree, because they don’t affect the visual output.

---

## Step 2: Style Resolution (Matching CSS to Elements)

Each DOM element is matched with CSS rules using **selectors**. Then the browser calculates the **computed style** (final value after applying cascading rules, inheritance, and defaults).

Example:

```html
<style>
  body { color: black; }
  p { color: blue; font-size: 16px; }
</style>
<body>
  <p>Hello World</p>
</body>
```

- The `<p>` element’s `color` = `blue` (specific rule overrides inherited black)
    
- The `font-size` = `16px` (explicitly defined)
    

The Render Tree node for `<p>` will have a **resolved style** like:

```json
{
  "display": "block",
  "color": "blue",
  "font-size": "16px",
  "margin": "16px 0"
}
```

---

## Step 3: Layout (Reflow)

Once the Render Tree is ready, the browser computes the **geometry** (size and position) of each node.

This step is called **layout** or **reflow**.

Example visual layout:

```plaintext
<body>
  ├── <h1> (0,0) size: 800x40
  └── <p> (0,50) size: 800x20
```

📏 **Key Layout Concepts:**

- Parent layout depends on children’s size.
    
- `%` values depend on parent dimensions.
    
- Changes to layout-affecting properties (width, margin, padding, font-size, etc.) trigger **reflow**.
    

---

## Step 4: Painting

After layout, the browser paints the pixels on the screen.

- The **Render Tree** nodes are traversed, and their visual styles (color, border, shadow, etc.) are drawn onto **layers**.
    
- Complex effects like shadows, opacity, and transforms may cause the browser to create **separate compositing layers**.
    

```plaintext
Layer 0 → background, text
Layer 1 → transformed elements (e.g., translateZ)
Layer 2 → fixed or positioned UI elements
```

Painting is done in **display lists**: sequences of paint commands like `drawRect`, `drawText`, etc.

---

## Step 5: Compositing

Modern browsers split rendering into **multiple layers** for performance.

- Layers are rasterized separately (possibly by GPU).
    
- Then they’re **composited together** in the correct stacking order (z-index, position, etc.).
    

```plaintext
[Compositing Process]
 ├── Paint Layers
 ├── GPU Rasterization
 └── Layer Composition → Final Frame on Screen
```

 **Performance Tip:**  
Avoid unnecessary reflows and repaints by:

- Minimizing layout thrashing (e.g., using `getBoundingClientRect()` in loops)
    
- Using CSS `transform` and `opacity` (these affect only compositing, not layout)
    
- Batch DOM changes (use `DocumentFragment` or `requestAnimationFrame`)
    

---

##  Quick Summary Table

|Stage|Input|Output|Notes|
|---|---|---|---|
|Tokenization|HTML bytes|Tokens|Parsing characters into meaningful tokens|
|Tree Construction|Tokens|DOM Tree|Structural representation|
|CSS Parsing|CSS text|CSSOM|Stylesheet object model|
|Render Tree Construction|DOM + CSSOM|Render Tree|Visual structure (only visible elements)|
|Layout|Render Tree|Geometry info|Determines position and size|
|Paint|Geometry|Layers with pixels|Draws colors, borders, shadows, text|
|Composite|Layers|Final image|Combines layers into a frame|

---

## Visualization Flow

```plaintext
HTML → Tokenizer → DOM
CSS → Parser → CSSOM
DOM + CSSOM → Render Tree → Layout → Paint → Composite → Screen
```

---

##  Developer Insight

- DOM changes trigger **reflow + repaint**.
    
- Style changes (e.g., `color`, `background`) trigger **repaint only**.
    
- Transform or opacity changes may trigger **compositing only** (fastest).
    

Understanding Render Tree helps developers **optimize page speed** and avoid costly reflows in animations or dynamic UI updates.

---

**In summary:**  
The **Render Tree** is the browser’s internal visual map combining structure (DOM) and style (CSSOM). It’s the foundation for layout, painting, and compositing — every visible pixel on the web page is derived from it.

---
