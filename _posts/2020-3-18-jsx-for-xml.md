---
title: 📚 XML Using JSX
long_title: Better XML in JavaScript using JSX
layout: blog
categories: 
    - jsx
    - xml
    - javascript 
    - typescript 
---

Working with XML in JS is a nightmare. JS wants you to work with JSON. There are many compilers from  JSON to XML but you then have to learn another format and may make mistakes when context switching. Using JSX removes this. We also gain the ability to compose XML fragments as if they were DOM nodes. We can create reusable components, extract them into modules, share them across projects, apply transformations, etc. It makes XML handling a first class function of the language with tooling support.

Why XML though? Well, many internation standards are written in XML (UBL XML for electronic trade and invoicing, SCXML for statecharts, etc). We can fight against this or embrace it as an advantage once we have the correct tooling in place.

### Main External Libs
- [JSX-XML pragma](https://github.com/smmoosavi/jsx-xml)
- [Babel](https://babeljs.io/)
- [babel-plugin-transform-jsx](https://github.com/calebmer/node_modules/tree/master/babel-plugin-transform-jsx)

### Glue Code

The output of the JSX transformation plugin is not the usual 
`(elName, attr, children)` that you get from other JSX transformers. 
JSX-XML pragma expects the normal output and so we can create a 
little adaptor like so:

```javascript
function jsx(jsxObject) {
  return JSXXML(
    jsxObject.elementName,
    jsxObject.attributes,
    jsxObject.children
  )
}
```

We then use this as the jsx pragma instead of JSXXML.

```js
const babel = require("@babel/standalone")

babel.registerPlugin('transform-jsx', require('babel-plugin-transform-jsx'))

const code = babel.transform(`
import {render, JSXXML} from 'jsx-xml'
import format from 'xml-formatter';
function jsx(jsxObject) {
  return JSXXML(
    jsxObject.elementName,
    jsxObject.attributes,
    jsxObject.children
  )
}
const node = <Invoice>
   <cbc:ID>123</cbc:ID>
   <cbc:IssueDate>2011-09-22</cbc:IssueDate>
   <cac:InvoicePeriod>
      <cbc:StartDate>2011-08-01</cbc:StartDate>
      <cbc:EndDate>2011-08-31</cbc:EndDate>
   </cac:InvoicePeriod>
   <cac:AccountingSupplierParty>
      <cac:Party>
         <cac:PartyName>
            <cbc:Name>Custom Cotter Pins</cbc:Name>
         </cac:PartyName>
      </cac:Party>
   </cac:AccountingSupplierParty>
   <cac:AccountingCustomerParty>
      <cac:Party>
         <cac:PartyName>
            <cbc:Name>North American Veeblefetzer</cbc:Name>
         </cac:PartyName>
      </cac:Party>
   </cac:AccountingCustomerParty>
   <cac:LegalMonetaryTotal>
      <cbc:PayableAmount currencyID="CAD">100.00</cbc:PayableAmount>
   </cac:LegalMonetaryTotal>
   <cac:InvoiceLine>
      <cbc:ID>1</cbc:ID>
      <cbc:LineExtensionAmount currencyID="CAD">100.00</cbc:LineExtensionAmount>
      <cac:Item>
         <cbc:Description>Cotter pin, MIL-SPEC</cbc:Description>
      </cac:Item>
   </cac:InvoiceLine>
</Invoice>
console.log(format(render(node)));
`, {
  plugins: [["transform-jsx", { "function": "jsx" }]],
  presets: ['es2015', 'es2016', 'es2017']
}).code;

console.log(code)
```

If we dig into what it output by Babble we can see that it does indeed use our JSX pragma. 

```js
"use strict";

var _jsxXml = require("jsx-xml");

var _xmlFormatter = _interopRequireDefault(require("xml-formatter"));

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

function jsx(jsxObject) {
  return (0, _jsxXml.JSXXML)(jsxObject.elementName, jsxObject.attributes, jsxObject.children);
}

var node = jsx({
  elementName: "Invoice",
  attributes: {},
  children: [jsx({
    elementName: "cbc:ID",
    attributes: {},
    children: ["123"]
  }), jsx({
    elementName: "cbc:IssueDate",
    attributes: {},
    children: ["2011-09-22"]
  }), jsx({
    elementName: "cac:InvoicePeriod",
    attributes: {},
    children: [jsx({
      elementName: "cbc:StartDate",
      attributes: {},
      children: ["2011-08-01"]
    }), jsx({
      elementName: "cbc:EndDate",
      attributes: {},
      children: ["2011-08-31"]
    })]
  }), jsx({
    elementName: "cac:AccountingSupplierParty",
    attributes: {},
    children: [jsx({
      elementName: "cac:Party",
      attributes: {},
      children: [jsx({
        elementName: "cac:PartyName",
        attributes: {},
        children: [jsx({
          elementName: "cbc:Name",
          attributes: {},
          children: ["Custom Cotter Pins"]
        })]
      })]
    })]
  }), jsx({
    elementName: "cac:AccountingCustomerParty",
    attributes: {},
    children: [jsx({
      elementName: "cac:Party",
      attributes: {},
      children: [jsx({
        elementName: "cac:PartyName",
        attributes: {},
        children: [jsx({
          elementName: "cbc:Name",
          attributes: {},
          children: ["North American Veeblefetzer"]
        })]
      })]
    })]
  }), jsx({
    elementName: "cac:LegalMonetaryTotal",
    attributes: {},
    children: [jsx({
      elementName: "cbc:PayableAmount",
      attributes: {
        currencyID: "CAD"
      },
      children: ["100.00"]
    })]
  }), jsx({
    elementName: "cac:InvoiceLine",
    attributes: {},
    children: [jsx({
      elementName: "cbc:ID",
      attributes: {},
      children: ["1"]
    }), jsx({
      elementName: "cbc:LineExtensionAmount",
      attributes: {
        currencyID: "CAD"
      },
      children: ["100.00"]
    }), jsx({
      elementName: "cac:Item",
      attributes: {},
      children: [jsx({
        elementName: "cbc:Description",
        attributes: {},
        children: ["Cotter pin, MIL-SPEC"]
      })]
    })]
  })]
});
console.log((0, _xmlFormatter.default)((0, _jsxXml.render)(node)));
```

The output of running the above code is a perfectly formed XML document (not neccessarily adhearing to the UBL spec but good enough for a demo).
```xml
<?xml version="1.0"?>
<Invoice>
    <cbc:ID>
        123
    </cbc:ID>
    <cbc:IssueDate>
        2011-09-22
    </cbc:IssueDate>
    <cac:InvoicePeriod>
        <cbc:StartDate>
            2011-08-01
        </cbc:StartDate>
        <cbc:EndDate>
            2011-08-31
        </cbc:EndDate>
    </cac:InvoicePeriod>
    <cac:AccountingSupplierParty>
        <cac:Party>
            <cac:PartyName>
                <cbc:Name>
                    Custom Cotter Pins
                </cbc:Name>
            </cac:PartyName>
        </cac:Party>
    </cac:AccountingSupplierParty>
    <cac:AccountingCustomerParty>
        <cac:Party>
            <cac:PartyName>
                <cbc:Name>
                    North American Veeblefetzer
                </cbc:Name>
            </cac:PartyName>
        </cac:Party>
    </cac:AccountingCustomerParty>
    <cac:LegalMonetaryTotal>
        <cbc:PayableAmount currencyID="CAD">
            100.00
        </cbc:PayableAmount>
    </cac:LegalMonetaryTotal>
    <cac:InvoiceLine>
        <cbc:ID>
            1
        </cbc:ID>
        <cbc:LineExtensionAmount currencyID="CAD">
            100.00
        </cbc:LineExtensionAmount>
        <cac:Item>
            <cbc:Description>
                Cotter pin, MIL-SPEC
            </cbc:Description>
        </cac:Item>
    </cac:InvoiceLine>
</Invoice>
```


Fragments require a bit more configuration to get working out of the box. Below is an example of them in action. 

```js
const babel = require("@babel/standalone")

babel.registerPlugin('transform-jsx', require('babel-plugin-transform-jsx'))

const code = babel.transform(`
import {render, JSXXML, Fragment} from 'jsx-xml'
import format from 'xml-formatter';
function jsx({elementName, attributes, children}) {
  return JSXXML(
    elementName,
    attributes,
    children
  )
}
function IssueDateComponent(props) {
  return (
    <Fragment>
        <cbc:IssueDate>{props.date}</cbc:IssueDate>
        <cbc:IssueDate>{props.date}</cbc:IssueDate>
    </Fragment>
  )
}
const element = <Invoice><IssueDateComponent date="18/4/18" /></Invoice>;
console.log(format(render(element)));
`, {
  plugins: [["transform-jsx", { "function": "jsx", "useVariables": "(Component|^Fragment)" }]],
  presets: ['es2015', 'es2016', 'es2017']
}).code;

console.log(code)
```


Working with XML like this allows us to create component, fragments and all the other features we've come to expect from working with JSX. This drastically simplifies the pipeline in generating XML documents. Making XML a first class citizen in a JSON world. 