# Typescript Updates highlights

## version 5.2 August 2023

- Easier method usage for unions of arrays.
- copying array methods; `toSorted, toSpliced, toReversed, with`. `Width` updates a single element.
- Inline Variable Refactoring: make a variable inline.

## version 5.1 June 2023

- Decoupled type-checking between JSX Elements and JSX Tag types. See new type JSX.ElementType.
- Linked Cursors for JSX Tags (enable in VSCode)

## version 5 March 2023

- Decorators for customization of classes and their members.
- `const` modifier for type parameters to replace `as const` to prevent mutations.
- multiple config files in extends. Allows base tsconfig.son that can be extended in different project.
- Since Typescript 2 (September 2016), typescript has supported enum literal types. Now TS can use computed enum members.
- Typescript 5 now supports moduleResolution `bundler`. This means that we tell typescript that our code is going to be bundled by another tool, and thus to loosen the rules with imports. Which means we don't need to use extensions.
- verbatimModuleSyntax: Simplifies type import handling.
