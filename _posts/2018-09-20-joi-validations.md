---
title: Custom Joi Validators
long_title: Custom Joi Validators
layout: blog
categories: 
    - joi 
    - javascript
    - validators
---

Building plugins for your validator should help you clean up the logic of your app. A lot of times people will write validators inline or add a service that will do the check and call it post validation. It then means you need to handle the error yourself outside the normal flow of the application. It also means that you cant (and probably wont) reuse the validation in another part of your app. Sometimes this is fine, not everything is reusable (despite what you read 😉).

```javascript
const baseJoi = require("joi");
const moment = require('moment');

const joiPasswordCheck = (joi) => {
  return {
      name: 'string',
      base: joi.string(),
      language: {
        datetime: 'must be a valid format, (e.g.): YYYY-MM-DD HH:MM:SS'
      },
      rules: [{
        name: 'datetime',
        validate(params, value, state, options) {
          console.log(params, value, state, options);
          
          if (moment(value).isValid()) {
            return value;
          } else {
            return this.createError('string.datetime', { value }, state, options);
          }
        }
      }]
    }
}

const joi = baseJoi.extend(joiPasswordCheck);

const createdAt = joi.string().datetime();

console.log(createdAt.validate('23/01/2019', { abortEarly: false }));

```