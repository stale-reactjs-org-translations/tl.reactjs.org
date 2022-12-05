---
id: introducing-jsx
title: Pagpapakilala sa JSX
permalink: docs/introducing-jsx.html
prev: hello-world.html
next: rendering-elements.html
---

Mangyaring tingnan ang variable declaration na ito:

```js
const element = <h1>Hello, world!</h1>;
```

Ang syntax na ito ay hindi isang string o HTML.

Ito ay tinatawag na JSX, at isa itong halimbawa ng syntax extension sa JavaScript. Ipinapayo naming gamitin ito kasama ng React para bigyan ng porma ang UI. Maihahalintulad ang JSX sa isang template language, ngunit maaari rin itong ipaghalo sa iba pang JavaScript na code.

Mula sa JSX, makabubuo tayo ng mga React "element". Ipapakita sa [susunod na kabanata](/docs/rendering-elements.html) ang pag-render ng JSX papunta sa DOM. Samantala, nasa sumusunod na bahagi ang mga halimbawa ng JSX para masimulan ang paggamit nito.

### Bakit JSX? {#bakit-jsx}

Kaakibat ng pagre-render ang iba pang mga ginagawa sa UI--kinakatawan ng React ang ganitong ideya. Halimbawa nito ang paghahandle sa mga event, ang mga pabago-bago sa state, at ang paghahanda sa pagpapakita ng mga datos. 

Imbis na paghiwalayin ang markup at logic sa magkabukod na file, [pinaghihiwalay ng React ang mga concern](https://en.wikipedia.org/wiki/Separation_of_concerns) gamit ang mga "component" na naglalaman ng parehong markup at logic, nang hindi masyadong kailangang dumependende sa iba pang mga file. Babalikan natin ang pagtalakay sa mga component sa [susunod na kabanata](/docs/components-and-props.html), ngunit kung pakiramdam mo na mukhang di tama ang paglalagay ng markup sa JS, baka magbago ang isip mo sa [panayam na ito](https://www.youtube.com/watch?v=x7cQ3mrcKaY). 

[Hindi kailangan ng JSX sa React](/docs/react-without-jsx.html), ngunit malaking tulong ito sa maraming tao para magkaroon ng ideya ukol sa hitsura ng UI sa loob ng JavaScript code. Maipapakita rin ng React ang mga error at warning nang malinaw at madaling basahin.

Ngayong naibigay na natin ang mga aspeto ng JSX, maaari na tayong magsimula!

### Paglalagay ng mga Expression sa JSX {#paglalagay-ng-mga-expression-sa-jsx}

Nag-declare tayo ng variable na `name` at inilagay natin ito sa JSX gamit ang mga curly brace sa sumusunod na halimbawa:  

```js{1,2}
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>;
```

Maaaring maglagay ng kahit anong [JavaScript expression](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#Expressions) na valid sa loob ng JSX; halimbawa nito ay `2 + 2`, `user.firstName`, o kaya naman ay `formatName(user)`.

Sa halimbawang ito, nilagay natin ang resulta ng pagtawag sa isang JavaScript function, `formatName(user)`, sa loob ng element na `<h1>`:

```js{12}
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);
```

**[Try it on CodePen](https://codepen.io/gaearon/pen/PGEjdG?editors=1010)**

Pinaghiwa-hiwalay natin ang JSX sa bawat linya para madali itong basahin. Nilagay rin natin sa loob ng mga ng mga panaklong (parenthesis) ang JSX. Hindi naman ito kailangan, ngunit naniniwala kami na maaaring maiwasan ang pagkalito dala ng [automatic semicolon insertion](https://stackoverflow.com/q/2846283) ng JavaScript.

### Ang JSX ay Isa Ring Expression {#ang-jsx-ay-isa-ring-expression}

Pagkatapos i-compile, nagiging JavaScript rin ang JSX--mga function call na nagre-return ng JavaScript object.

Ibig sabihin, maaaring gamitin ang JSX sa loob ng `if` statement at `for` loop, i-assign sa variable, gamitin bilang argument, at i-return mula sa mga function:

```js{3,5}
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

### Paglalagay ng Attribute sa JSX {#paglalagay-ng-attribute-sa-jsx}

Maaaring gamitin ang mga panipi (quotes) upang ipasa ang mga string bilang mga attribute:

```js
const element = <a href="https://www.reactjs.org"> link </a>;
```

Maaari ring gamitin ang mga curly brace upang maglagay ng JavaScript expression para maging attribute:

```js
const element = <img src={user.avatarUrl}></img>;
```

Hindi maaaring gamitin parehas ang curly brace at mga panipi sa paglalagay ng attribute. Kailangang piliin ang panipi
para sa mga string, at curly brace para sa mga expression.

>**Warning:**
>
>Dahil mas malapit ang JSX sa JavaScript kaysa sa HTML, gumagamit ng `camelCase` ang React DOM para sa mga attribute imbis na HTML.
>
>Halimbawa, ang `class` ay nagiging [`className`](https://developer.mozilla.org/en-US/docs/Web/API/Element/className) sa JSX, at `tabindex` naman ay nagiging [`tabIndex`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLElement/tabIndex).

### Paglalagay ng Children sa JSX {#paglalagay-ng-children-sa-jsx}

Maaaring isara ang empty tag gamit ang `/>`, parang sa XML:

```js
const element = <img src={user.avatarUrl} />;
```

Puwedeng magkaroon ng children ang mga JSX tag:

```js
const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
);
```

### Maiiwasan ang Injection Attack Gamit ang JSX {#maiiwasan-ang-injection-attack-gamit-ang-jsx}

Ligtas ang paglalagay ng mga user input sa JSX:

```js
const title = response.potentiallyMaliciousInput;
// This is safe:
const element = <h1>{title}</h1>;
```

[Ine-escape](https://stackoverflow.com/questions/7381974/which-characters-need-to-be-escaped-on-html) ng React DOM ang kahit anong value na nasa loob ng JSX bago ito i-render. Sinisiguro nito na hindi mapapayagan ang mga value galing sa labas ng application na makapasok. Nagiging string ang lahat ng JSX saka ito ire-render, dahilan kaya naiiwasan ang mga [XSS (cross-site-scripting)](https://en.wikipedia.org/wiki/Cross-site_scripting) attack.

### Kinakatawan ng JSX ang mga Object {#kinakatawan-ng-jsx-ang-mga-object}

Kino-compile ng Babel ang JSX na maging mga pagtawag sa React.createElement()`.

Magkapareho ang mga halimbawang ito:

```js
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

```js
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```

Tsine-check ng `React.createElement()` ang mga code sa mga posibleng pagmulan ng bug at iniiwasan ito, pero sa huli, ganito ang magiging kalalabasan ng pagtawag ng function:

```js
// Note: simplipikado ang ganitong structure
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

Ito ang mga "React element"--pwede mo silang isipin na detalye sa mga nakikita sa screen. Binabasa ng React ang ganitong mga object para mag-render ng DOM at gawin itong up-to-date.

Ipapakita natin ang pag-render ng React element sa DOM sa [susunod na kabanata](/docs/rendering-elements.html).

>**Tip:**
>
>Ipinapayo na gamitin ang ["Babel" language definition](https://babeljs.io/docs/editors) para sa iyong editor, upang mahighlight nang tama ang iyong mga ES6 at JSX code.
