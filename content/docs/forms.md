---
id: forms
title: Forms
permalink: docs/forms.html
prev: lists-and-keys.html
next: lifting-state-up.html
redirect_from:
  - "tips/controlled-input-null-value.html"
  - "docs/forms-zh-CN.html"
---

<<<<<<< HEAD
Ang HTML form elements ay medyo iba kung gumana kumpara sa ibang DOM elements ng React, dahil natural na pinanatili ng form elements ang ilang internal state. Halimbawa, ang form na ito sa simpleng HTML ay tumatanggap ng isang name:
=======
HTML form elements work a bit differently from other DOM elements in React, because form elements naturally keep some internal state. For example, this form in plain HTML accepts a single name:
>>>>>>> f3baa6d075c8de475b688abf035d7054bc8a9606

```html
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

Ang form na ito ay mayroong default na HTML form behavior na pag-browse sa bagong pahina kapag ipinasa ang form. Kapag gusto mo ang ganitong behavior sa React, gumagana na ito. Kadalasan, mas nakakatulong kung merong JavaScript function na nagha-handle sa pagpasa ng form at mayroong access sa na-enter na data ng user sa form. Ang karaniwang paraan upang makamit ito ay gamit ang pamamaraang tinatawag na "controlled components".

## Controlled Components {#controlled-components}

Sa HTML, ang form elements tulad ng `<input>`, `<textarea>`, at `<select>` ay karaniwang pinanatili ang kanilang sariling state at ina-update ito batay sa input ng user. Sa React, ang mutable state ay karaniwang pinanatili sa state property ng components, at ina-update lang gamit ang [`setState()`](/docs/react-component.html#setstate).

Mapagsasama natin ang dalawa kapag ang React state ay ginawang "nag-iisang mapagkukunan ng katotohanan". Tapos ang React component na nagre-render ng form ay kinokontrol din ang nangyayari sa form sa mga kasunod na input ng user. Ang input form element na ang value ay kontrolado ng React sa ganitong pamamaraan ay tinatawag na "controlled component".

Halimbawa, kung nais nating gawin ang nakaraang halimbawa na pag-log ng name kapag ito ay ipinasa, pwede natin isulat ang form bilang isang controlled component:

```javascript{4,10-12,21,24}
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/VmmPgp?editors=0010)

Dahil ang `value` attribute ay naka set sa ating form element, ang displayed value ay palaging magiging `this.state.value`, na kung saan ang React state ay nagiging nag-iisang mapagkukunan ng katotohanan. Dahil ang `handleChange` ay gumagana sa bawat keystroke para i-update ang React state, ang displayed value ay maa-update habang nag ta-type ang user.

Gamit ang controlled component, ang value ng input ay palaging nakabatay sa React state. Kahit na nangangahulugan ito na kailangan mong mag-type ng kaunti pang code, maaari mo na ngayong maipasa ang value sa iba pang mga UI elements, o i-reset ito mula sa ibang mga event handlers.

## Ang textarea Tag {#the-textarea-tag}

Sa HTML, tinutukoy ng `<textarea>` element ang kanyang text sa children nito:

```html
<textarea>
  Hello there, this is some text in a text area
</textarea>
```

Sa React, ang `<textarea>` ay sa halip gumagamit ng `value` attribute. Sa ganitong pamamaraan, ang form na gumagamit ng `<textarea>` ay pwedeng isulat na halos katulad sa isang form na gumagamit ng isang linyahan na input:

```javascript{4-6,12-14,26}
class EssayForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 'Please write an essay about your favorite DOM element.'
    };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('An essay was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Essay:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

Pansinin mo na ang `this.state.value` ay initialized sa constructor, upang ang text area ay magsisimula na mayroong ilang text dito.

## Ang select Tag {#the-select-tag}

Sa HTML, ang `<select>` ay gumagawa ng drop-down na listahan. Halimbawa, itong HTML ay gumawa ng drop-down na listahan ng mga lasa:

```html
<select>
  <option value="grapefruit">Grapefruit</option>
  <option value="lime">Lime</option>
  <option selected value="coconut">Coconut</option>
  <option value="mango">Mango</option>
</select>
```

Tandaan na ang Coconut option ay initially na naka select, dahil sa `selected` attribute. Ang React, sa halip na gamitin itong `selected` attribute, gumagamit ito ng `value` attribute sa root nito na `select` tag. Ito ay mas makakatulong sa isang controlled component dahil kailangan mo lamang itong i-update sa iisang lugar. Halimbawa:

```javascript{4,10-12,24}
class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'coconut'};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Your favorite flavor is: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Pick your favorite flavor:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">Grapefruit</option>
            <option value="lime">Lime</option>
            <option value="coconut">Coconut</option>
            <option value="mango">Mango</option>
          </select>
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/JbbEzX?editors=0010)

Sa kabuuan, ginagawa ito upang ang `<input type="text">`, `<textarea>`, at `<select>` lahat ay maghalintulad na gumagana - lahat sila ay tumatanggap ng `value` attribute na maari gamitin para mag implement ang isang controlled component.

> Paalala
>
> Maaari kang magpasa ng isang array sa `value` attribute na kung saan pinapayagan kang pumili ng maraming mga options sa isang `select` tag:
>
>```js
><select multiple={true} value={['B', 'C']}>
>```

## Ang file input Tag {#the-file-input-tag}

Sa HTML, ang `<input type="file">` ay pinapahintulutan ang user na pumili ng isa o higit pang mga files sa kanilang device storage upang mai-upload sa isang server o mamanipula ng JavaScript gamit ang [File API](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications).

```html
<input type="file" />
```

Dahil ang value nito ay maari lamang basahin, ito ay isang **uncontrolled** component sa React. Itatalakay ito kasama ang iba pang uncontrolled components sa mga [susunod na mga kabanata](/docs/uncontrolled-components.html#the-file-input-tag).

## Pag-handle ng Maraming Inputs {#handling-multiple-inputs}

Kapag kailangan mo mag handle ng maraming controlled `input` elements, maaari kang magdagdag ng `name` attribute sa bawat element at hayaan ang handler function na pumili kung ano ang gagawin batay sa value ng `event.target.name`.

Halimbawa:

```javascript{15,18,28,37}
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

[**Subukan sa CodePen**](https://codepen.io/gaearon/pen/wgedvV?editors=0010)

Tandaan kung papaano ginamit ang ES6 [computed property name](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names) na syntax para ma-update ang key ng state ayon sa ibinigay na name na input:

```js{2}
this.setState({
  [name]: value
});
```

Ito ay katumbas nitong ES5 na code:

```js{2}
var partialState = {};
partialState[name] = value;
this.setState(partialState);
```

Dahil ang `setState()` ay kusang [pinagsasama ang partial state sa current state](/docs/state-and-lifecycle.html#state-updates-are-merged), kailangan na lamang nating tawagin ito sa mga nabago nitong bahagi.

## Controlled Input na Null ang Value {#controlled-input-null-value}

Sa pag-specify ng value prop sa isang [controlled component](/docs/forms.html#controlled-components),
pinipigilan nito ang user na baguhin ang input maliban kung nais mo. Kapag nag-specify ka ng `value` pero ang input ay maaari paring i-edit, baka hindi mo sinasadyang na ma-set ang `value` sa `undefined` o `null`.

Ipinapakita ito ng sumusunod na code. (Ang input ay naka-lock sa una ngunit maaaring ma-edit ito pagkatapos ng isang maikling pagkaantala.)

```javascript
ReactDOM.render(<input value="hi" />, mountNode);

setTimeout(function() {
  ReactDOM.render(<input value={null} />, mountNode);
}, 1000);

```

## Mga Alternatibo sa Controlled Components {#alternatives-to-controlled-components}


Minsan ay masyadong matrabaho kung gamitin ang mga controlled components, dahil kailangan mo magsulat ng isang event handler para sa bawat paraan na maaaring mabago ang data at i-pipe ang lahat ng state ng input sa pamamagitan ng isang React component. Maaari itong maging nakakainis kapag nagko-convert ka ng isang dati nang codebase sa React o nag i-integrate ng React application sa isang non-React na library. Sa mga ganitong panahon, baka gusto mong tignan ang kabanata ukol sa [uncontrolled components](/docs/uncontrolled-components.html), isang alternatibong pamamaraan sa pag-implement ng input forms.

## Mga Subok nang mga Solusyon {#fully-fledged-solutions}

Kung naghahanap ka ng kumpletong solusyon kasama ang validation, pagsubaybay sa mga binisitang mga fields, at pag-handle ng pagpasa ng form, ang [Formik](https://jaredpalmer.com/formik) ang isa sa mga patok na pagpipilian. Subalit, ito ay binuo sa parehong mga prinsipyo ng mga controlled components at pag-manage ng state - kaya huwag magpabaya na malaman ang mga ito.
