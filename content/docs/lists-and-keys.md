---
id: lists-and-keys
title: Lists at Keys
permalink: docs/lists-and-keys.html
prev: conditional-rendering.html
next: forms.html
---

<<<<<<< HEAD
Una, balikan natin kung paano mo trina-transform ang lists sa Javascript.
=======
> Try the new React documentation.
> 
> These new documentation pages teach modern React and include live examples:
>
> - [Rendering Lists](https://beta.reactjs.org/learn/rendering-lists)
>
> The new docs will soon replace this site, which will be archived. [Provide feedback.](https://github.com/reactjs/reactjs.org/issues/3308)


First, let's review how you transform lists in JavaScript.
>>>>>>> ba290ad4e432f47a2a2f88d067dacaaa161b5200

Sa code sa ibaba, gumamit tayo ng [`map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) function na tumatanggap ng array ng `numbers` at dinodoble ang values nito. Inaassign natin ang bagong balik na array gamit ang `map()` sa variable na `doubled` at inila-log ito:

```javascript{2}
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((number) => number * 2);
console.log(doubled);
```

Makikita ang resultang ito sa console `[2, 4, 6, 8, 10]`.

Sa React, ang pagta-transform ng arrays sa lists [elements](/docs/rendering-elements.html) ay halos magkaparehas.

### Pag-rerender ng Multiple Components {#rendering-multiple-components}

Maaaring bumuo ng koleksyon ng elements at [isama ito sa JSX](/docs/introducing-jsx.html#embedding-expressions-in-jsx) gamit ang curly braces `{}`.

Sa ibaba, inilu-loop natin ang `numbers` array gamit ang JavaScript [`map()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) function. Ibinabalik natin ang `<li>` element sa bawat item. Pang-huli, ina-assign natin ang resultang array ng elements sa `listItems`:

```javascript{2-4}
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);
```

<<<<<<< HEAD
Isinasama natin ang buong `listItems` array sa loob ng `<ul>` element, at [inirerender ito sa DOM](/docs/rendering-elements.html#rendering-an-element-into-the-dom):
=======
Then, we can include the entire `listItems` array inside a `<ul>` element:
>>>>>>> ba290ad4e432f47a2a2f88d067dacaaa161b5200

```javascript{2}
<ul>{listItems}</ul>
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/GjPyQr?editors=0011)

Ang code na yon ay nagdi-display ng bullet list ng numbers mula 1 hanggang 5.

### Basic List Component {#basic-list-component}

Kadalasan ay magrerender ka ng list sa loob ng [component](/docs/components-and-props.html).

Maari nating irefactor ang nakaraang halimbawa sa component na tumatanggap ng array ng `numbers` at naglalabas ng list ng elements.

```javascript{3-5,7,13}
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<NumberList numbers={numbers} />);
```

Kapag pinagana mo ang code na ito, makikita mo ang babala na tungkol sa dapat na pagdaragdag ng key para sa list items. Ang "key" ay espesyal na string attribute na kailangan mong isama kapag gumagawa ka ng lists ng elements. Tatalakayin natin ito sa susunod na kabanata kung bakit ito importante.

Tayo'y mag-assign ng `key` sa ating list ng items sa loob ng `numbers.map()` para ang isyu ng nawawalang key.

```javascript{4}
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/jrXYRR?editors=0011)

## Keys {#keys}

Ang keys ay tumutulong sa React upang matukoy kung alin sa mga items ang nagbago, nadagdagan, o natanggal. Ang mga keys ay dapat ibigay sa mga elements sa loob ng array upang magkaroon ang mga elements ng stable na pagkakakilanlan.

```js{3}
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li key={number.toString()}>
    {number}
  </li>
);
```

Ang pinakamahusay na paraan sa pagpili ng key ay ang paggamit ng string na natatanging kumikilala sa list item at naiiba ito sa ibang items. Ang key ay kadalasang ang IDs galing sa iyong data.

```js{2}
const todoItems = todos.map((todo) =>
  <li key={todo.id}>
    {todo.text}
  </li>
);
```

Kapag wala kang mapiling stable IDs para sa rendered items, maaring gamitin ang item index para sa key bilang last resort.

```js{2,3}
const todoItems = todos.map((todo, index) =>
  // Gawin lang ito kung walang kang stable IDs
  <li key={index}>
    {todo.text}
  </li>
);
```

<<<<<<< HEAD
Hindi namin inirerekomenda ang paggamit ng indexes para sa keys kung ang order ng items ay maaring magbago. Ito ay maaring magdulot ng negatibong epekto sa performace at maaring magdulot ng issue sa component state. Tingnan ang artikulo ni Robin Pokorny's na [in-depth explanation on the negative impacts of using an index as a key](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318). Kung hindi ka maglalagay ng key sa list items, bilang default, ang React ay gagamitin ang indexes bilang keys.
=======
We don't recommend using indexes for keys if the order of items may change. This can negatively impact performance and may cause issues with component state. Check out Robin Pokorny's article for an [in-depth explanation on the negative impacts of using an index as a key](https://robinpokorny.com/blog/index-as-a-key-is-an-anti-pattern/). If you choose not to assign an explicit key to list items then React will default to using indexes as keys.
>>>>>>> ba290ad4e432f47a2a2f88d067dacaaa161b5200

Ito ang [in-depth na pagpapaliwanag kung bakit kailangan ng keys](/docs/reconciliation.html#recursing-on-children) kung ikaw ay interesadong matuto pa.

### Pag-eextract ng Components gamit ang Keys {#extracting-components-with-keys}

Ang keys ay may katuturan lamang sa kontexto ng surrounding array.

Halimbawa, kung [mag-eeextract](/docs/components-and-props.html#extracting-components) ka ng `ListItem` component, dapat mong ilagay ang key sa `<ListItem />` elements ng array sa halip na sa `<li>` element sa loob ng `ListItem`.

**Halimbawa: Ang maling paggamit ng Key**

```javascript{4,5,14,15}
function ListItem(props) {
  const value = props.value;
  return (
    // Mali! hindi na dapat ilagay ang key dito:
    <li key={value.toString()}>
      {value}
    </li>
  );
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // Mali! Dapat mayroong nakalagay na key dito:
    <ListItem value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}
```

**Halimbawa: Ang tamang paggamit ng Key**

```javascript{2,3,9,10}
function ListItem(props) {
  // Tama! Hindi na kailangang maglagay ng key dito:
  return <li>{props.value}</li>;
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // Tama! Dapat na ilagay ang key dito sa loob ng array.
    <ListItem key={number.toString()} value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/ZXeOGM?editors=0010)

Laging tandaan na ang mga elements sa loob ng `map()` ay kailangan ng keys.

### Ang mga Keys ay dapat na naiiba sa ibang keys {#keys-must-only-be-unique-among-siblings}

<<<<<<< HEAD
Ang mga Keys ay dapat na naiiba sa ibang keys. Ngunit hindi naman ito kailangang maging globally unique.  Sa madaling salita, maari tayong gumamit ng magkaparehong keys kapag mayroon tayong dalawang magkaibang arrays:
=======
Keys used within arrays should be unique among their siblings. However, they don't need to be globally unique. We can use the same keys when we produce two different arrays:
>>>>>>> 0057efa12c1aa2271ef80d7a84d622732bdfa85c

```js{2,5,11,12,19,21}
function Blog(props) {
  const sidebar = (
    <ul>
      {props.posts.map((post) =>
        <li key={post.id}>
          {post.title}
        </li>
      )}
    </ul>
  );
  const content = props.posts.map((post) =>
    <div key={post.id}>
      <h3>{post.title}</h3>
      <p>{post.content}</p>
    </div>
  );
  return (
    <div>
      {sidebar}
      <hr />
      {content}
    </div>
  );
}

const posts = [
  {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},
  {id: 2, title: 'Installation', content: 'You can install React from npm.'}
];

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<Blog posts={posts} />);
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/NRZYGN?editors=0010)

Ang keys ay nagsisilbing gabay kay React pero hindi ito naipapasa sa components. Kung kailangan mo ng magkaparehong value sa iyong component, explicitly na ipasa ito bilang prop na may ibang pangalan:

```js{3,4}
const content = posts.map((post) =>
  <Post
    key={post.id}
    id={post.id}
    title={post.title} />
);
```

Sa halimbawa sa itaas, ang `Post` component ay babasahin `props.id`, pero hindi ang `props.key`.

### Pag-eembed ng map() sa JSX {#embedding-map-in-jsx}

Sa mga halimbawa sa itaas ay hiwalay nating idineklara ang `listItems` variable at isinama natin ito sa JSX:

```js{3-6}
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <ListItem key={number.toString()}
              value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}
```

Pinahihintulutan sa JSX ang [pag-eembed ng expression](/docs/introducing-jsx.html#embedding-expressions-in-jsx) sa loob ng curly braces upang ma-inline natin ang resulta ng `map()`:

```js{5-8}
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />
      )}
    </ul>
  );
}
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/BLvYrB?editors=0010)

Madalas ay nagreresulta ito ng mas malinaw na code, bagamat ang istilong ito ay maaaring maabuso. Tulad sa JavaScript, nasasayo ang desisyon kung may halaga bang i-extract na variable sa ngalan ng readability. Ilagay sa isip na kung ang `map()` body ay masyado nang madami, mas madanda sigurong [i-extract ang component](/docs/components-and-props.html#extracting-components).