---
id: thinking-in-react
title: Paano mag-isip sa React
permalink: docs/thinking-in-react.html
redirect_from:
  - 'blog/2013/11/05/thinking-in-react.html'
  - 'docs/thinking-in-react-zh-CN.html'
prev: composition-vs-inheritance.html
---

Ang React, sa aming opinyon, ay nangunguna pagdating sa pagbuo ng malaki at mabilis na Web apps gamit ang Javascript na mabisa naming nagamit sa Facebook at Instagram.

Isa sa malaking parte ng React ay kung paano ka magisip habang bumubuo ka ng apps. Sa dokyumentong ito, ipapakita namin sa inyo ang thought process sa pagbuo ng searchable product data table gamit ang React.

## Magumpisa gamit ang isang Mock {#start-with-a-mock}

Isipin mong mayroon na tayong JSON API at mock galing sa ating designer. Ang mock ay ganito ang itsura:

![Mockup](../images/blog/thinking-in-react-mock.png)

Ang ating JSON API ay magbabalik ng data tulad nito:

```
[
  {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},
  {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},
  {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},
  {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},
  {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},
  {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}
];
```

## Ika-1 Hakbang: Hatiin ang UI sa isang Component Hierarchy {#step-1-break-the-ui-into-a-component-hierarchy}

Ang una mong dapat gawin ay gumuhit ng mga kahon sa paligid ng bawat component (at subcomponent) sa mock at bigyan mo lahat ito ng pangalan. Kung ikaw ay may katuwang na designer, maaring nagawa na ito, at maaari kang makipag-ugnayan sa kanila. Maaari mo ding pangalanan ang iyong React components gamit ang kanilang Photoshop layer name.

Ngunit paano mo malalaman kung anong mga component ang iyong gagawin? Gamitin ang iyong pamamaraan sa pagpasya bago ka gumawa ng bagong function o object. O kaya gamitin ang [single responsibility principle](https://en.wikipedia.org/wiki/Single_responsibility_principle) na nagsasabing ang isang component ay dapat isa lang ang tungkulin. Kung ang component ay lumalaki, dapat na mahati ito sa mas maliliit pang subcomponents.

Dahil madalas kang nagpapakita ng JSON data model sa user, mapapasin mo kung tama ang pagkakagawa ng iyong model, ang iyong UI (at pati ang iyong component structure) ay sasangayon ng maayos. Sa kadahilanang ang UI at data models ay sumusunod sa kaparehong *information architecture*. Hatiin ang iyong UI sa mga components, kung saan ang bawat component ay tugma sa iyong data model.

![Diagram showing nesting of components](../images/blog/thinking-in-react-components.png)

<<<<<<< HEAD
Makikita mo na mayroon tayong limang components sa ating app. Naka-italicized ang data sa bawat kinakatawang component.
=======
You'll see here that we have five components in our app. We've italicized the data each component represents. The numbers in the image correspond to the numbers below.
>>>>>>> 4022f9774b8dd93d863fd6bca6447b1f03ae3979

  1. **`FilterableProductTable` (orange):** naglalaman ng kalahatan sa ating halimbawa
  2. **`SearchBar` (blue):** tumatanggap ng *user input*
  3. **`ProductTable` (green):** nagpapakita at pini-filter ang *data collection* batay sa *user input*
  4. **`ProductCategoryRow` (turquoise):** nagpapakita ng heading sa bawat *category*
  5. **`ProductRow` (red):** nagpapakita ng row sa bawat *product*

Kung titingnan mo ang `ProductTable`, mapapansin mo na ang table header (na naglalaman ng "Name" at "Price" labels) ay hindi ang kanyang sariling component. Ito ay isang preference, at mayroong argumento na gawin ito sa alinmang paraan ninanais. Sa halimbawa, isinama namin ito sa `ProductTable` dahil ito ay kasama ng rendering ng *data collection* na `ProductTable` ang responsable. Ngunit, kung ay header ay lumaki at nagiging kumplikado (e.g., kung daragdagan namin ng sorting), mas makahulugan kung magkakaroon ito ng sarili nyang `ProductTableHeader` component.

Ngayon na nalaman na natin ang mga components sa ating mock, ayusin na natin ang hierarchy nito. Ang components na nasa ilalim ng isa pang component sa mock ay magiging child sa hierarchy.

  * `FilterableProductTable`
    * `SearchBar`
    * `ProductTable`
      * `ProductCategoryRow`
      * `ProductRow`

## Ika-2 Hakbang: Gumawa ng Static Version sa React {#step-2-build-a-static-version-in-react}

<p data-height="600" data-theme-id="0" data-slug-hash="BwWzwm" data-default-tab="js" data-user="lacker" data-embed-version="2" class="codepen">Subukan sa Pen ang  <a href="https://codepen.io/gaearon/pen/BwWzwm">Paano mag-isip sa React: Ika-2 Hakbang</a> sa <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

Ngayon na mayroon ka nang component hierarchy, oras na para gawin ito sa iyong app. Ang pinakamadaling paraan ay bumuo ng version na tatanggap ng data model at irerender sa UI ngunit wala itong interactivity. Mas makakabuti na paghiwalayin ang mga prosesong ito dahil ang pagbuo ng static version ay nangangailangan ng maraming typing at kaunting pag-iisip, at ang pag-dagdag naman ng interactivity ay nangangailangan ng malalim na pag-iisip at kaunting pagta-type. Malalaman nating kung bakit.

Upang makabuo ka ng static version ng app na magrerender ng iyong data model, gugustuhin mong bumuo muna ng components na maaaring gamitin pa ng iba pang components at magpasa ng data gamit ang *props*. Ang *props* ay paraan ng pagpasa ng data mula sa parent papunta sa child component. Kung ikaw ay pamilyar sa concept ng *state*, **huwag kang gumamit ng state** upang bumuo ng static version. Ang State ay nakareserba lamang para sa interactivity, yan ay, sa mga data na nagbabago o *dynamic*.

Maari kang bumuo top-down or bottom-up. Yan ay, maaari kang magsimulang bumuo ng mga components pababa sa hierarchy (i.e. umpisa sa `FilterableProductTable`) o ang mga mas mabababa pa dito (`ProductRow`). Sa simpleng halimbawa, mas madali ang magsimula sa taas papuntang baba (top-down), at sa malalaking proyekto, mas madali namang magsimula sa baba papuntang taas (bottom-up) at magsulat ng tests habang ikaw ay bumubuo ng proyekto.

Sa pagtatapos ng hakbang na ito, magkakaroon ka na ng library ng reusable components na magrerender ng iyong data model. Ang mga components ay mayroon lamang na `render()` methods dahil ito ay static version ng iyong app. Ang mga component sa taas ng iyong hierarchy (`FilterableProductTable`) ay tatanggap ng iyong data model sa pamamagitan ng prop. Kung babaguhin mo ang laman ng data model at tatawagin ulit ang `ReactDOM.render()`, ang UI ay mag-uupdate. Makikita mo kung paano mag-update ang iyong UI at kung saan may mangyayaring pagbabago. Ang **one-way data flow** ng React (tinatawag din bilang *one-way binding*) ay nagpapanatili ng pagiging modular at mabilis nito.

<<<<<<< HEAD
Sumangguni sa [React docs](/docs/) kung kailangan mong i-execute ang hakbang na ito.
=======
Refer to the [React docs](/docs/getting-started.html) if you need help executing this step.
>>>>>>> 4022f9774b8dd93d863fd6bca6447b1f03ae3979

### Maikling Interlude: Props vs State {#a-brief-interlude-props-vs-state}

May dalawang tipo ng "model" data sa React: props at state. Ito ay mahalagang maintindihan ang pakakaiba ng dalawa; pasadahan [ang official React docs](/docs/state-and-lifecycle.html) kung hindi ka sigurado sa pagkakaiba ng dalawa. Tingnan din ang [FAQ: Ano ang pagkakaiba ng state and props?](/docs/faq-state.html#what-is-the-difference-between-state-and-props)

## Ika-3 Hakbang: Kilalanin ang Minimal (pero kumpletong) Representasyon ng UI State {#step-3-identify-the-minimal-but-complete-representation-of-ui-state}

Upang maging interactive ang iyong UI, kailangan mong mag-trigger ng pagbabago sa laman ng iyong data model. Makakamit nito ang React gamit ang **state**.

Upang mabuo ang iyong app ng tama, kailangan mong mag-isip ng maliit ng set ng mutable state na kailangan ng iyong app. Ang susi dito ay [DRY: *Don't Repeat Yourself*](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself). Hanapin ang pinakamaliit na representasyon ng state na kailangan ng iyong application at isipin ang iba pang kailangan mo on-demand. Halimbawa, kung bubuo ka ng TODO list, panatilihin ang array ng TODO items; huwag panatilihin ang hiwalay na state variable para sa count. Sa halip, kapag guto mong irender ang TODO count, kuhanin ang length ng TODO items array.

<<<<<<< HEAD
Isipin ang lahat ng kailangan ng iyong data sa ating halimbawa. Mayroon tayong:
=======
Think of all the pieces of data in our example application. We have:
>>>>>>> 4022f9774b8dd93d863fd6bca6447b1f03ae3979

  * Ang orihinal na listahan ng mga products
  * Ang search text na itinala ng user
  * Ang value ng checkbox
  * Ang filtered list ng products

Tingnan natin isa-isa at tukuyin kung ano dito ang state. May tatlong tanong na makakatulong sa pagtukoy:

  1. Ito ba ay ipinasa mula sa parent gamit ang props? Kung oo, maaring hindi ito state.
  2. Hindi ba ito nagbabago habang lumilipas ang panahon? Kung oo, maaring hindi ito state.
  3. Mako-compute ba ito base sa ibang state o props sa iyong component? Kung oo, maaring hindi ito state.

Ang orihinal na listahan ng products ay ipinapasa bilang props, kaya ito ay hindi state. Ang search text at ang checkbox ay tila state dahil nagbabago sila sa paglipas ng pahahon at hindi kinompute. Panghuli, ang filtered list ng products ay hindi state dahil ito ay nako-compute sa pamamagitan ng pagsasama ng orihinal na listahan ng produkto kasama ang search text at ang value ng checkbox.

Sa wakas, ang ating state ay:

  * Ang search text ng user na itinala
  * Ang value ng checkbox

## Ika-4 na Hakbang: Suriin kung saan titira ang iyong State {#step-4-identify-where-your-state-should-live}

<p data-height="600" data-theme-id="0" data-slug-hash="qPrNQZ" data-default-tab="js" data-user="lacker" data-embed-version="2" class="codepen">Subukan sa Pen ang <a href="https://codepen.io/gaearon/pen/qPrNQZ">Isipin sa React: Ika-4 na Hakbang</a> sa <a href="https://codepen.io">CodePen</a>.</p>

OK, nalaman na natin ang pinakamaliit na set ng app state. Susunod naman, kailangan nating suriin kung anong component ang nagmu-mutate na, o *nagmamay-ari* ng, state.

Tandaan: Ang lahat ng tungkol sa React ay one-way data flow pababa sa component hierarchy. Ang mga state sa component ay maaring hindi pa malinaw agad. **Ito ay madalas na hindi naiintindihan ng mga baguhan sa React,** kaya sundan ang mga hakbang na ito para mas maintindihan:

Para sa bawat state ng iyong application:

  * Suriin ang bawat component na nag-rerender ng kahit ano base sa state na yon.
  * Hanapin ang pangkaraniwang may-ari ng component (madalas ay ito ang iisang component sa itaas ng lahat ng components na kailangan ng state sa hierarchy).
  * Alinman sa pangkaraniwang may-ari o ibang component na mas mataas sa hierarchy ay nararapat magmay-ari ng state.
  * Kung hindi mo mahanap ang component kung saan may katuturan na magmay-ari ng state, gumawa ng bagong component para lang ariin ang state at isama ito kung saan man sa hierarchy basta sa itaas ng pangkaraniwang may-ari ng component.

Pasadahan natin ang pamamaraang ito sa ating application:

  * `ProductTable` ay kailangan ng filter ng product list base sa state at ang `SearchBar` naman ay kailangan ipakita ang search text at checked state.
  * Ang pangkaraniwang may ari ng component ay `FilterableProductTable`.
  * Mas makabuluhang kung ang filter text at checked value ay nasa `FilterableProductTable`

 
Astig, ngayon ay napagdesisyunan na natin na ang state ay nasa `FilterableProductTable`. Una, idagdag ang instance property `this.state = {filterText: '', inStockOnly: false}` sa `constructor` ng `FilterableProductTable` para ipakita ang initial state ng iyong application. Pagkatapos, ipasa ang `filterText` at `inStockOnly` sa `ProductTable` at `SearchBar` bilang prop. Panghuli, gamiting ang mga props na ito para ifilter ang rows sa `ProductTable` at iset ang values ng form fields sa `SearchBar`.

Makikita mo na kung paano gumagana ang iyong application: iset ang `"ball"` sa `filterText` at i-refresh ang iyong app. Makikita mo na ang data table ay nag-update na ng tama.

## Ika-5 Hakbang: Pagdaragdag ng Inverse Data Flow {#step-5-add-inverse-data-flow}

<p data-height="600" data-theme-id="0" data-slug-hash="LzWZvb" data-default-tab="js,result" data-user="rohan10" data-embed-version="2" data-pen-title="Paano mag-isip sa React: Ika-5 Hakbang" class="codepen">Subukan sa Pen ang <a href="https://codepen.io/gaearon/pen/LzWZvb">Paano mag-isip sa React: Ikaw-5 Hakbang</a> sa <a href="https://codepen.io">CodePen</a>.</p>

Sa ngayon, nabuo na natin ang app na nag-rerender ng tama gamit ang function ng props at ang state ang bumababa ng maayos sa hierarchy. Ngayon ay oras na para gawing ng data flow sa kasalungat na paraan: ang kailailalimang form components sa hierarchy ay kailangan i-update ag state sa `FilterableProductTable`.

Ang React ay ginagawa ang ganitong paraan ng data flow upang tulungan ka na maintindihan kung paano gumaga ang iyong program, pero kailangan ng kaunting pagta-type kumpara sa tradisyunal na two-way data binding.

<<<<<<< HEAD
Kung susubukan mong itype or icheck ang box sa kasalukuyang version ng halimbawa, makikita mo na walang epekto ito sa React. Ito ay inaasahan, dahil sineset natin ang `value` prop ng input na palagiang kapareho ng `state` na ipinasa galing sa `FilterableProductTable`.
=======
If you try to type or check the box in the previous version of the example (step 4), you'll see that React ignores your input. This is intentional, as we've set the `value` prop of the `input` to always be equal to the `state` passed in from `FilterableProductTable`.
>>>>>>> 4022f9774b8dd93d863fd6bca6447b1f03ae3979

Isipin kung ano ang gusto nating mangyari. Nais nating masigurado na kapag binago ng user ang form, maa-update ang state ayon sa user input. Sa kadahilanang ang mga components ay dapat na iupdate ang kanilang sariling state, ang `FilterableProductTable` ay ipapasa ang callbacks sa `SearchBar` na magsisimula kapag mag-uupdate ang state. Maaari nating gamitin ang `onChange` event sa inputs para maabisuhan tayo nito. Ang mga callbacks na naipasa ng `FilterableProductTable` ay tatawagin ang `setState()`, ang app ay maa-update.

## At natapos din {#and-thats-it}

Sana naman ay nagbigay ito sayo ng kaliwanagan kung paano mag-isip sa pagbuo ng components at application gamit ang React. Habang ito ay pinapa-type ka ng mas marami kumpara sa iyong nakagawian, tandaan na ang nababasang code, at mas madaling basahin ang modular, na explicit na code. Sa pagsisimula mo nang pagbuo ng malaking library ng components, mas lalo mong mapapahalagahan ang explicitness at modularity, at gamit ang konsepto ng code reuse, ang iyong code ay mas lalong magiging maiksi. :D
