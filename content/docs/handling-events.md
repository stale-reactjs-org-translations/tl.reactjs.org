---
id: handling-events
title: Pamamahala ng mga Events
permalink: docs/handling-events.html
prev: state-and-lifecycle.html
next: conditional-rendering.html
redirect_from:
  - "docs/events-ko-KR.html"
---

Ang pamamahala ng mga events sa React elements ay parehong-pareho sa pamamahala ng mga events sa mga DOM elements. Mayroon lamang mga pagkakaiba sa syntax

* Ang mga events sa React ay pinapangalanan gamit ang camelCase, imbis na lowercase.
* Gamit ang JSX, ikaw ay magpapasa ng function bilang event handler, taliwas sa nakasanayan na string.

Halimbawa, ang HTML:

```html
<button onclick="activateLasers()">
  Activate Lasers
</button>
```

ay may kaunting pagkakaiba sa React.

```js{1}
<button onClick={activateLasers}>
  Activate Lasers
</button>
```
<<<<<<< HEAD
Isa pang pagkakaiba ay hindi ka pwede magbalik ng `false` upang mapigil ang default behavior sa React. Kailangan mong lantarang tawagin ang `preventDefault`. Halimbawa, gamit ang HTML, upang mapigilan ang default behavior ng link tuwing magbubukas ng bagong pahina, maaari mong isulat:
=======

Another difference is that you cannot return `false` to prevent default behavior in React. You must call `preventDefault` explicitly. For example, with plain HTML, to prevent the default form behavior of submitting, you can write:
>>>>>>> 0057efa12c1aa2271ef80d7a84d622732bdfa85c

```html
<form onsubmit="console.log('You clicked submit.'); return false">
  <button type="submit">Submit</button>
</form>
```

Sa React, ito ay magiging:

```js{3}
function Form() {
  function handleSubmit(e) {
    e.preventDefault();
    console.log('You clicked submit.');
  }

  return (
    <form onSubmit={handleSubmit}>
      <button type="submit">Submit</button>
    </form>
  );
}
```

Dito, ang `e` ay isang synthetic event. Binigyang depinisyon ng React ang mga synthetic events ayon sa [W3C spec](https://www.w3.org/TR/DOM-Level-3-Events/), kaya hindi mo na kailangan mag-alala sa cross-browser compatibility. Ang events sa React ay hindi katulad ng native events. Tingnan ang reference guide ng [`SyntheticEvent`](/docs/events.html) upang mas maraming malaman.

Tuwing gagamit ng React, hindi mo na kailangang tawagin ang `addEventListener` tuwing magdadagdag ng listener sa isang DOM element matapos itong magawa. Sa halip, kailangan mo lang maglagay ng listener sa element bago ito mairender.

Tuwing ikaw ay gagawa ng component gamit ang [ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes), ang madalas na pattern ay ang paggawa ng event handler bilang method ng isang class. Halimbawa, itong `Toggle` component ay nagrerender ng button na naglalayon na magpalit sa pagitan ng "ON" at "OFF" na states:



```js{6,7,10-14,18}
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prevState => ({
      isToggleOn: !prevState.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/xEmzGg?editors=0010)

Kailangan mong maging maingat sa kahulugan ng `this` sa mga JSX callbacks. Sa Javascript, ang mga class methods ay hindi [bound](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_objects/Function/bind) ayon sa default. Kapag nakalimutan mong ibind ang `this.handleClick` at pinasa mo ito sa `onClick`, ang `this` ay magiging `undefined` kapag ito ay natawag.

Hindi ito behavior na sa React lang matatagpuan, ito ay parte kung [paano gumagana ang functions sa Javascript](https://www.smashingmagazine.com/2014/01/understanding-javascript-function-prototype-bind/). Kadalasan, kapag ikaw ay tumukoy ng method nang walang `()` kasunod nito, tulad ng `onClick={this.handleClick}`, kailangan mong ibind ang method na ito.

<<<<<<< HEAD
Kung ang pagtawag ng `bind` ay nakakainis para sayo, mayroong dalawang paraaan para matakasan ito. Kung ginagamit mo ang experimental [public class fields syntax](https://babeljs.io/docs/plugins/transform-class-properties/), pwede mong gamitin ang mga class fields upang tamang ibind ang callbacks.
=======
If calling `bind` annoys you, there are two ways you can get around this. You can use [public class fields syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/Public_class_fields#public_instance_fields) to correctly bind callbacks:
>>>>>>> ea9e9ab2817c8b7eff5ff60e8fe9b649fd747606

```js{2-6}
class LoggingButton extends React.Component {
  // This syntax ensures `this` is bound within handleClick.
  handleClick = () => {
    console.log('this is:', this);
  };

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

Ang syntax na ito ay pinapagana ayon sa default ng [Create React App](https://github.com/facebookincubator/create-react-app).

Kung hindi ka gumagamit ng class fields syntax, maaari kang gumamit ng [arrow function](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions) sa callback:

```js{7-9}
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // This syntax ensures `this` is bound within handleClick
    return (
      <button onClick={() => this.handleClick()}>
        Click me
      </button>
    );
  }
}
```

Ang problema sa ganitong syntax ay magkakaroon ng ibang callback tuwing ang `LoggingButton` ay narerender.  Sa kabuuan, ayos lamang ito. Ngunit, kung ang callback ay pinasa bilang prop sa lower components, itong mga component na ito ay maaaring gumawa ng extra re-rendering. Pinapayuhan namin na mag-bind sa constructor o kaya gumamit ng class fields syntax, upang maiwasan ang ganitong problema sa performance.

## Pagpapasa ng mga arguments sa event handlers{#passing-arguments-to-event-handlers}

Sa loob ng loop, nakasanayan na ang pagpapasa ng extra parameter sa isang event handler. Halimbawa, kung ang `id` ay para sa hanay na ID, maaring gamitin ang sumusunod:

```js
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

Ang dalawang linya sa itaas ay magkapareho lamang, at gumagamit ng [arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) at [`Function.prototype.bind`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_objects/Function/bind).

Sa parehong halimbawa, ang argument `e` ay kumakatawan sa React event na ipapasa bilang ikalawang argument kasunod ng ID. Gamit ang arrow function, kailangan natin itong lantarang ipasa, ngunit kapag `bind`, ang mga karagdagang mga arguments ay automatic na naipapasa.
 