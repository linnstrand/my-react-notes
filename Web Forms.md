# Web Forms

As a lot of self taught developers, I have gotten thrown into framework at the deep end.

This project uses Angular/Vue/React? Ok read the quickstart and look up solutions to tasks as you go.

Building a form? Google "Build a form in React" and you probably will get a ton of helpful guides telling you how to do it in their favorite stack. Probably Formik or React Hook Forms. You find a example and tweak it to your needs.

But why do you need a library? Can it really be so hard to create basic form support with React and the built in tools?
Yes. Yes it can.
I got a challenge to use only a Next.js project, HTML and CSS for a very simple form with a few constraints.

1. Name. Required.
2. Email. Required and email format.
3. Postal code. Swedish post code format.
4. A second name. Could not be the same as the first name.

Name is easy. Input of text type and required.

Email. Type email and required.
Aaand it happily take `a` without complaint. All `type="email"` does is inform browsers that this is a email field. Your autocomplete suggests a email you often use. Mobile devices adjust the keyboard for email. But no validation.
Ok lucky we have the `pattern` attribute. It accepts an regex and now we're talking! On submit the browser gives you a warning that the field is not formatted correctly. But the error message "Please match the format requested". Well that's not very helpful. I want the user to actually get the format spelled out.

Ok, there is support for custom message. Here is how it can look. The error message comes in two versions. If a required value is not filled in, the tip spells that out. Otherwise we default to a custom message.

```typescript
const onInvalid = (event: InvalidEvent<HTMLInputElement>) => {
  event.target.validity.valueMissing
    ? `${name} is required`
    : event.target.setCustomValidity(validationTip);
};
```

We now have validation with tips. Good!
But it is limited. What if the field constraint can't be a regex? What if we want styling? Is it even any accessibility at all? Using "title" to describe the pattern offers some support, but is it truly enough?

Modern users expects feedback on blur, with colors and aria-labels. Validation rules should also be clearly visible and not require initial guesses.

Like with date pickers, re-inventing the wheel is painful and resource intense.
