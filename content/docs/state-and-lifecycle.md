---
id: state-and-lifecycle
title: State and Lifecycle
permalink: docs/state-and-lifecycle.html
redirect_from:
  - "docs/interactivity-and-dynamic-uis.html"
prev: components-and-props.html
next: handling-events.html
---

Ang pahinang ito ay magpapaliwanag sa concept ng state and lifecycle sa React component. Makikita ang mas [detalyadong reference sa component API dito](/docs/react-component.html).

Balikan natin ang halimbawa ng ticking clock [sa nakaraang section](/docs/rendering-elements.html#updating-the-rendered-element). Sa [Rendering Elements](/docs/rendering-elements.html#rendering-an-element-into-the-dom), natutunan natin ang one way na pag-update sa UI. Tinawag natin ang `ReactDOM.render()` upang palitang ang rendered output:

```js{8-11}
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/gwoJZk?editors=0010)

Sa section na ito, matututunan natin kung papaano gumawa ng `Clock` component na magiging reusable at encapsulated. Isesetup nito ang kanyang sariling timer at ia-update nito ang oras kada segundo.

Simulan natin ang pag-eencapsulate sa `Clock` component tulad nito:

```js{3-6,12}
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/dpdoYR?editors=0010)

Ngunit, nakaligtaan nito ang isang mahalagang pangangailangan: karapatdapat na ang pag-setup ng `Clock` timer at UI update sa bawat segundo ay mapatupad sa `Clock` component mismo.

Mas angkop sanang isulat ito ng isang beses lamang at hayaang ang `Clock` ay mag-update ng sarili nya:

```js{2}
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

Upang ito ay mapatupad, kailangan nating magdagdag ng "state" sa `Clock` component.

Ang state ay katulad ng props, pero ito ay private at nako-control lang ng component.

## Pag-convert ng Function upang maging Class {#converting-a-function-to-a-class}

Maaring i-convert ang function component tulad ng `Clock` upang maging class sa loob ng limang hakbang:

1. Gumagawa ng [ES6 class](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes), na may kaparehong pangalang, at naka-extend sa `React.Component`.

2. Magdagdag ng `render()` method na walang laman.

3. Ilipat ang lamang ng function sa loob ng `render()` method.

4. Ipalit ang `this.props` sa lahat ng`props` sa loob ng `render()`.

5. Tanggaling ang mga natitirang blanking function declaration.

```js
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/zKRGpo?editors=0010)

Ang `Clock` ay isa nang class at hindi na isang function.

Ang `render` method ay tatawagin sa bawat mangyayaring update, ngunit habang nagrerender ang `<Clock />` sa kaparehong DOM node, tanging iisang instang lang ng `Clock` class ang magagamit. Ito ang magpapahintulot sa ating upang makapagdagdag ng features tulad ng local state at lifecycle methods.

## Pagdaragdag ng Local State sa Class {#adding-local-state-to-a-class}

Gagawin nating state ang `date` na dating props sa loob ng tatlong hakbang:

1) Palitan ang `this.props.date` ng `this.state.date` sa loob ng `render()` method:

```js{6}
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

2) Magdagdag ng [class constructor](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes#Constructor) na nag-aasign ng `this.state`:

```js{4}
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

Tingnan mabuti kung paano natin ipinapasa ang `props` sa base constructor:

```js{2}
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
```

Ang class components ay dapat na palaging tawagin ang base constructor `props`.

3) Tanggaling ang `date` prop mula sa `<Clock />` element:

```js{2}
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

Ibabalik natin mamaya ng timer code sa component.

Ganito ang magiging resulta:

```js{2-5,11,18}
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/KgQpJd?editors=0010)

Susunod, gagawin natin ang `Clock` na magkaroon ng sarili nitong timer at mag-update ito sa bawat segundo.

## Pagdagdag ng Lifecycle Methods sa Class {#adding-lifecycle-methods-to-a-class}

Sa application na mayroon maraming components, napakahalaga na ma-free up ang resources na ginagamit ng components kapag ito ay hindi na ginagamit na.

Gusto nating [mag-set up ng timer](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/setInterval) kapag ang `Clock` ay narender sa DOM sa unang pagkakataon. Ang tawag dito ay "mounting" sa React.

Gusto din nating [i-clear ang timer](https://developer.mozilla.org/en-US/docs/Web/API/WindowTimers/clearInterval) kapag ang DOM na ginawa ng `Clock` ay natanggal. Ang tawag dito ay "unmounting" sa React.

Pwede tayong mag-declare ng special methods sa component class para patakbuhin ang code kapag na-mount at unmount ang component.

```js{7-9,11-13}
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {

  }

  componentWillUnmount() {

  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

Ang tawag sa mga method na ito ay "lifecycle methods".

Ang `componentDidMount()` method ay tumatakbo pagkapatapos mag-render ng output ang component sa DOM. Ito ay magandang pagkakataon para mag-setup ng timer:

```js{2-5}
  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }
```

Tingnan mabuti kung paano tayo nagse-save ng timer ID sa `this` (`this.timerID`).

Habang ang `this.props` ay sine-setup ng React at ang `this.state` ay may special meaning, malaya kang magdagdag ng ibang fields sa class manually kung kailangan mong mag-store ng kahit ano na hindi kasama sa data flow (tulad ng timer ID).

Tayo ay ite-tear down ang timer sa `componentWillUnmount()` lifecycle method:

```js{2}
  componentWillUnmount() {
    clearInterval(this.timerID);
  }
```

Sa wakas, tayo ay mag-iimplement ng method na tatatawaging `tick()` na papatakbuhin ng `Clock` component bawat segundo.

Gagamitin nito ang `this.setState()` para mag-schedule ng update sa component local state:

```js{18-22}
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/amqdNA?editors=0010)

Ngayon ay nag-titick na ang orasan sa bawat segundo.

Tayo'y mabilis na balikan kung ano ang ating ginawa at ang order kung kailan tinawag ang mga method:

1) Kapag ang `<Clock />` ay ipinasa sa `ReactDOM.render()`, ang React ay tinatawag ang constructor ng `Clock` component. Dahil sa ang `Clock` ay kailangang mag-display ng current time, nag-iinitialize ito ng `this.state` na may object kasama ang current time. I-uupdate natin ang state na ito mamaya.

2) Ang React ngayon ay tinatawag ang `render()` method ng `Clock` component. Ito ang paraan ng React kung papano nito nalalaman kung ano ang i-didisplay sa screen. Ang React ay ina-update ang DOM para maging kapareho ng ang render output ng `Clock` component.

3) Kapag ang `Clock` output ay nailagay na sa DOM, ang React ay tinatawag ang `componentDidMount()` lifecycle method. Sa loob nito, ang `Clock` component ay nagsasabi sa browser na i-setup ang timer para tawagin ang `tick()` method bawat segundo. 

4) Ang browser ay tinatawag ang `tick()` method bawat segundo. Sa loob nito, ang `Clock` component ay ini-schedule ang a UI update sa pamamagitan ng pagtawag sa `setState()` na may object lamang ang current time. Salamat sa `setState()` call, ang React ay alam na nagbago na ang state, at tinatawag nito ang `render()` method ulit upang matutunan nito ang ilalabas sa screen. Sa pagkakataong ito, ang `this.state.date` sa `render()` method ay magiging iba, at ang render output ay ilalabas ang updated time. Ang React ay i-uupdate ang DOM ng maayos.

5) Kung ang `Clock` component ay mawawala sa DOM, ang React ay tatawagin ang `componentWillUnmount()` lifecycle method para mahinto ang timer.

## Tamang paggamit ng State {#using-state-correctly}

Mayroon tatlong bagay na dapat mong malamay sa `setState()`.

### Huwag madaliang i-modify ang State {#do-not-modify-state-directly}

Halimbawa, ito ay hindi ire-render muli ang component:

```js
// Wrong
this.state.comment = 'Hello';
```

Sa halip, gumamit ng `setState()`:

```js
// Correct
this.setState({comment: 'Hello'});
```

Ang tanging lugar na maaring mag-assign ng `this.state` ay sa constructor.

### Ang pag-update ng State ay maaring Asynchronous {#state-updates-may-be-asynchronous}

Ang React ay maaring tawagin ang maraming `setState()` minsanan upang makamit ang magandang performance.

Dahil ang `this.props` at `this.state` ay maaaring mag-update asynchronously, hindi ka dapat umasa sa mga value para i-calculate ang next state.

Halimbawa, ang code na ito ay hindi gagana sa pag-update ng counter:

```js
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

Para maayos ito, gumamit ng ikalawang form ng `setState()` na tumatanggap ng function sa halip na object. Ang function ay tatanggap ng previous state sa unang argument, at ang props sa oras ng pag-update ay magiging ikalawang argument:

```js
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```

Gumamit tayo ng [arrow function](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions) sa itaas, pero gagana din ito gamit ang regular function:

```js
// Correct
this.setState(function(state, props) {
  return {
    counter: state.counter + props.increment
  };
});
```

### State Updates are Merged {#state-updates-are-merged}

Kapag tinawag ang `setState()`, ang React ay pinagsasama ang object na iyong ibinigay at ang current state.

Halimbawa, ang iyong state ay maaring maglaman ng iilang independent variables:

```js{4,5}
  constructor(props) {
    super(props);
    this.state = {
      posts: [],
      comments: []
    };
  }
```

Pagkatapos ay maaari mong i-update ang mga ito nang independently sa magkakahiwalay na `setState ()` na tawag:

```js{4,10}
  componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    });
  }
```

Ang merging ay shallow, kaya ang `this.setState ({comments})` ay iniwang buo ang `ito.state.post`, ngunit ganap na pumapalit sa` this.state.comments`.

## Ang Data ay nagflo-Flow Pababa {#the-data-flows-down}

Ang parent o mga child component ay hindi alam kung ang component ay stateful o stateless, at hindi nito kailangan intindihin kung na-define ba ito na function or class.

Ito ang dahilan kung bakit ang state ay kadalasang local o encapsulated ang tawag. Matatawag lang ito sa component na nagmamay-ari dito at hindi sa ibang component.

Ang component ay maaring ipasa ang state nito bilang props sa kanyang mga child component:

```js
<FormattedDate date={this.state.date} />
```

Ang `FormattedDate` component ay tumatanggap ng `date` sa kanyang props at hindi nito alam kung ito ay galing sa state ng `Clock`, mula sa mga props ng `Clock`, o isinulat manually:

```js
function FormattedDate(props) {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
}
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/zKRqNB?editors=0010)

Kadalasang tawag dito ay "top-down" o "unidirectional" data flow. Ang anumang state ay palaging pag-aari ng partikular na component, at anumang data o UI na na-derive sa state ay makakaapekto lamang sa mga component na nasa ibaba nito.

Kung isisiping mo, ang component tree ay isang waterfall ng props, ang bawat component state ay katulad ng iba pang water source na pinagsasama ito sa isang punto pero dumadaloy din ito pababa.

Para ipakita na ang lahat ng component ay totoong isolated, maari tayong gumawa ng `App` component na nagrerender ng tatlong `<Clock>`:

```js{4-6}
function App() {
  return (
    <div>
      <Clock />
      <Clock />
      <Clock />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/vXdGmd?editors=0010)

Ang bawat `Clock` sine-setup ang kanyang sariling timer at ina-update ito independently.

Ang mga React apps, maging stateful man o stateless ang component ay itinuturing na implementation detail ng component na maaring magbago sa paglipas ng panahon. Maari kang gumamit ng stateless component sa loob ng stateful components, at kabaliktaran.
