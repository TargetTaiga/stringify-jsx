# stringify-jsx
JSX adaptation as a template system for non-React projects. Allowing to use JSX as a template everywhere adopting IDE's JSX highlight and formatting features.

## TLDR;
Transforming JSX:
```jsx harmony
class MyElement {
    render() {
        let title = "Hello World!";
        return <div>{title}</div>;
    }
}
```
Into this:
```js
class MyElement {
    render() {
        let title = "Hello World!";
        return `<div>${title}</div>`;
    }
}
```

Also by default transforms JSX html attributes:
```jsx harmony
class MyElement {
    render() {
        let myClass = "action";
        return <label className={myClass} htmlFor="button"></label>;
    }
}
```
Into regular html:
```js
class MyElement {
    render() {
        let myClass = "action";
        return `<label class="${myClass}" for="button"></label>`;
    }
}
```

## Quick start
```
npm i stringify-jsx
```
```js
stringifyJsx('let title = "Hello World!";let html = <div>{title}</div>;').code
// let title = "Hello World!";let html = `<div>${title}</div>`;
```
[Usage examples](https://github.com/TargetTaiga/rollup-plugin-stringify-jsx/tree/master/example)

## Options
```js
stringifyJsx('<div></div>', {
    // Preserve whitespaces between tags, default => false
    preserveWhitespace: false,
    // Custom attributes replacement functionality 
    customAttributeReplacements: {},
    customAttributeReplacementFn: void 0
}, { /* babel options */ })
```
Read more about [babel configuration](https://babeljs.io/docs/en/options).

## Custom attributes replacement
Pass ``customAttributeReplacements`` or ``customAttributeReplacementFn`` to options to adjust replacements. If ``customAttributeReplacementFn`` is passed ``customAttributeReplacements`` is ignored.

#### customAttributeReplacements
```js
stringifyJsx('<div className="myClass" value="hello world!"></div>', {
    customAttributeReplacements: {
        'value': 'data-value'
    }
}).code
// `<div class="myClass" data-value="hello world!"></div>`;
```

#### customAttributeReplacementFn
```js
stringifyJsx('<div className="myClass" value="hello world!"></div>', {
    customAttributeReplacementFn: (nodePath, defaultReplacement) => {
        if (defaultReplacement) {
            return defaultReplacement;
        }
        return 'x-' + nodePath.node.name;
    }
}).code
// `<div class="myClass" x-value="hello world!"></div>`
```
``nodePath`` is a [@babel/traverse's path](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/en/plugin-handbook.md#paths). 
``defaultReplacement`` is a default transformation (such as className => class, htmlFor => html).

## Source maps
Code and source maps are being generated by [@babel/generator](https://babeljs.io/docs/en/babel-generator). By default source map generation is turned off, to turn it on additional option should be provided:
```js
stringifyJsx('let title = "Hello World!";let html = <div>{title}</div>;', {}, {
    sourceMaps: 'inline'
}).code
// let title = "Hello World!";
// let html = `<div>${title}</div>`;
// //# sourceMappingURL=data:application/json;charset=utf-8;base64,eyJ2ZXJzaW9uIjozLCJzb3VyY2VzIjpbInVua25vd24iXSwibmFtZXMiOlsidGl0bGUiLCJodG1sIl0sIm1hcHBpbmdzIjoiQUFBQSxJQUFJQSxLQUFLLEdBQUcsY0FBWjtBQUEyQixJQUFJQyxJQUFJLHdCQUFSIiwic291cmNlc0NvbnRlbnQiOlsibGV0IHRpdGxlID0gXCJIZWxsbyBXb3JsZCFcIjtsZXQgaHRtbCA9IDxkaXY+e3RpdGxlfTwvZGl2PjsiXX0=
```

## Tagged templates ambiguity
To provide an ability to use stringify-jsx with tagged template literals (and build upon it libraries like [lit-html](https://lit-html.polymer-project.org/)) all function calls that contain JSX markup as argument are being transformed into tagged template literals.

This call:
```jsx harmony
html(<div>JSX Markup!</div>);
```
Will be transformed to:
```js
html`<div>JSX Markup!</div>`; 
```
If it's necessary to pass string transformed from JSX as function argument - assign JSX to a variable first:
```jsx harmony
const markup = <div>JSX Markup!</div>;
html(markup);
```  
So function call in resulting code will remain unchanged:
```js
const markup = `<div>JSX Markup!</div>`;
html(markup);
``` 

## lit-html
Due to support of tagged template literals and custom attribute replacements this tool can be used together with [lit-html](https://lit-html.polymer-project.org).
Explore [example project](https://github.com/TargetTaiga/lit-project-template) for more information.

## Notes
* Does not modify self-closing tags 

## Babel plugin
The core of stringify-jsx was moved to [babel-plugin-transform-stringify-jsx](https://github.com/TargetTaiga/babel-plugin-transform-stringify-jsx). If inline usage is not necessary please consider using combination babel + babel-plugin-transform-stringify-jsx. Explore [example project](https://github.com/TargetTaiga/lit-project-template).

## Other plugins
* [rollup-plugin-stringify-jsx](https://github.com/TargetTaiga/rollup-plugin-stringify-jsx)

## TODO
- [x] Babel plugin
- [x] Tests (babel-plugin contains core so tests are located there)
- [ ] Typescript
