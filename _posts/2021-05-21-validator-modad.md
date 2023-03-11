---
title: Validator Monad
long_title: Angular Reactive Forms Validator Monad For All
layout: blog
categories:
    - angular
    - javascript 
    - typescript
    - validators
---

This is an example of getting the same sort of validator definition that you have in angular (yeah who knew angular uses monads!!).

Needs a good bit more work.
- should automatically wrap the return value in `Failure` and `Success`.
- should not need to return an array. Should be an array by default.


```js
const {Success, Failure} = require( 'folktale/validation');
const R = require('ramda');

const isRequired = (form, field) => !!form[field].trim()
    ? Success([])
    : Failure(["This field is required"])

const isEmail = (form, field) => /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(form[field])
    ? Success([])
    : Failure([`${form[field]} doesn't seem to be an email`])

const isConfirmed = (fieldToConfirm, what) => (form, confirmField) => form[fieldToConfirm] === form[confirmField]
    ? Success([])
    : Failure([`${what} don't match`])

const mapEntries = require('folktale/core/object/map-entries')

const validate = (form, rules) => mapEntries(
    rules,
    ([field, fieldRules]) => {
    
        const possibleIssues = fieldRules.map(r => r(form, field))
                                         .reduce(R.concat, Success())
                                         .matchWith({
                                            Success: ({ value }) => value,
                                            Failure: ({ value }) => value
                                         })
        return [
            field,
            possibleIssues
        ]
    },
    (result, key, value) => R.assoc(key, value, result)
)

const form = {
    username: 'Jernej',
    email: 'jernej.sila@gmail',
    password: 'pass',
    password_confirmation: ''
}

const rules = {
    username:              [isRequired],
    email:                 [isRequired, isEmail],
    password:              [isRequired],
    password_confirmation: [isRequired, isConfirmed('password', 'Passwords')]
}

const errors = validate(form, rules)
```

```
{
    "username": [],
    "email": [
        "jernej@gmail doesn't seem to be an email"
    ],
    "password": [],
    "password_confirmation": [
        "This field is required",
        "Passwords don't match"
    ]
}
```