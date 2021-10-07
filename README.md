<h1>Programming Style Guides</h1>

# Table of contents
- [Table of contents](#table-of-contents)
- [General](#general)
    + [Avoid literals, e.g. string, number](#avoid-literals--eg-string--number)
    + [Use index file is possible](#use-index-file-is-possible)
    + [File naming](#file-naming)
    + [BEM css class naming](#bem-css-class-naming)
    + [Naming styles](#naming-styles)
    + [Naming convention](#naming-convention)
    + [Use views/screens/pages instead of containers](#use-viewsscreenspages-instead-of-containers)
    + [Group components if necessary](#group-components-if-necessary)
    + [Avoid copy and paste](#avoid-copy-and-paste)
    + [Avoid versioning](#avoid-versioning)
    + [Encapsulate API call](#encapsulate-api-call)
    + [Use spell-checking plugin](#use-spell-checking-plugin)
    + [Liskov substitution](#liskov-substitution)
- [Typescript](#typescript)
    + [Functional component](#functional-component)
    + [Useful types](#useful-types)
- [References](#references)

# General
### Avoid literals, e.g. string, number
Create constants or enums instead of using literals as it can utilize typings and thus easier to manage.
```ts
// Good
enum Directions = {
  LEFT = 'LEFT',
  RIGHT = 'RIGHT',
};
const Colors = {
  RED: 'red',
  BLUE: 'blue',
};
foo(Colors.BLUE);
bar(Directions.LEFT);

// Bad
foo('blue');
bar('left');
```

### Use index file is possible
If there is only a single file, you can simply create the file without a folder.
```bash
# Good
components
├── MyComponent
    ├── index.tsx
    └── styles.scss

# Good. If there is only one file, simply create a file instead of a folder.
components
└── MyComponent.tsx
   
# Bad
components
├── MyComponent
    ├── MyComponent.tsx
    └── MyComponent.scss
```
### File naming
```ts
// kebab-case for public assets, e.g. `public/img/bg-image.png`
import './bg-image.png';     

// camelCase for non react component files, e.g. `helper.ts`
import './helper';         

// PascalCase for react component files, e.g. `MyComponent.tsx`
import './MyComponent.tsx';  
```

### BEM css class naming
BEM css class name should follow format: `block__elem--modifier`

```scss
// Good
.login-form__header--large {}
.file-upload__drop-zone__msg {}

// Bad
.login_form__header--large {}
.file-upload--drop-zone--msg {}
```

### Naming styles
| Styles         | Category                                                           |
|----------------|--------------------------------------------------------------------|
| UpperCamelCase | class / interface / type / enum / decorator / type parameters      |
| lowerCamelCase | variable / parameter / function / method / property / module alias |
| CONSTANT_CASE  | global constant values, including enum values                      |

```ts
// Constants
const Colors = {
  RED: 'RED',
  PURPLE_BLUE: 'PURPLE_BLUE',
};

// Enums
enum Genders = {
  MALE: 'MALE',
  FEMALE: 'Female',
};

// Constant
const API_KEY = '<api_key>';

// Class
class ApiService {}

// React functional component
const MyComponent = () => {};

// Type
type Gender = 'male' | 'female';

// Interface
interface User {
  name: string;
};

// Variable
const result = foo();

// Function
const foo = () => {};
```

### Naming convention
| Category | Convention                                                                                                |
|----------|-----------------------------------------------------------------------------------------------------------|
| Boolean  | should/is/has/will + action. E.g. `shouldUpdate`, `isSubmitting`, `hasLoggedIn`                           |
| Function | action + target. e.g. `fetchData`                                                                         |
| Event    | on/handle + target + action, e.g. `<MyComponent onMouseMove={handleMouseMove} />`, `onRegistrationSubmit` |

- keep it simple, e.g. `emma-signup` → `signup`
- avoid inconsistency, e.g. `input.idcard.hkid` , `hkId` , `emmaSubmitFindPolicyByHKID`
- capitalize two-letter acronyms, e.g. `getIO` (but not for 3 or more letters, e.g. `getDob`)
- avoid misleading naming, e.g. `const errorCode = { errorMsg: { message: 'some error msg' } }`. 

### Use views/screens/pages instead of containers
In the context of react, a container often refers to container component (opposite to presentational component).
With the introduction of react hooks, this methodology of dividing components into presentation vs container might have been deprecated.
However we should still avoid confusion like this.

```bash
# Good
screens
└── Home
    ├── index.tsx
    └── styles.scss
   
# Bad
containers
└── Home
    ├── index.tsx
    └── styles.scss
``` 

### Group components if necessary
If components are related, you should consider grouping them into a folder.
```bash
# Good
components
└── form
    ├── fields
    |    ├── DateField
    |    └── NameField
    └── inputs 
         ├── TextInput
         └── SelectInput
    
# Bad
components
├── DateField
├── NameField
├── TextInput
└── SelectInput
```

### Avoid copy and paste
Don't repeat yourself. If same piece of code appears twice, it is a signal that you might refactor and create a shared component/function.
```ts
// Good
const Card = (props: { header: string, content: string }) => {
  return (
    <div className="card">
      <div className="card__header">{header}</div>
      <div className="card__content">{content}</div> 
    </div>
  );
};
const MyComponent = () => {
  return (
    <div>
      <Card header="This is card A" content="Content A" />
      <Card header="This is card B" content="Content B" />
      <Card header="This is card C" content="Content C" />
    </div>
  )
};

// Bad
const MyComponent = () => {
  return (
    <div>
      <div className="card">
        <div className="card__header">This is card A</div>
        <div className="card__content">Content A</div> 
      </div>
      <div className="card">
        <div className="card__header">This is card B</div>
        <div className="card__content">Content B</div> 
      </div>
      <div className="card">
        <div className="card__header">This is card C</div>
        <div className="card__content">Content C</div> 
      </div>
    </div>
  )
};
```

### Avoid versioning
Keeping mulitple versions of code is confusing as it usually introduces mixture of V1 and V2 codes.
If it is not possible to switch from one version to another with small changes, it is usually not an idea to keep both versions in this way.
If multiple versioning is required, you can do so with git branches.
```bash
# Good
forgetPassword

# Ok if switching from v1 to v2 is easy
v2/components/forgetPassword

# Bad
forgetPasswordV2
```

### Encapsulate API call
Wrap API call and response data transformation. 
So that the data fetching logics can be encapsulated.
```ts
// encapsulate user fetching, changing internal logics will not affect how this function is used
const fetchUser = async (userId: string) => {
  const result = await fetch(USER_API_URL)
    .then(res => res.json());
  
  const user: User = transform(result);
  return user;
};

const user: User = await fetchUser('emma'); // safely typed as User, does not care about how user is fetched
```

### Use spell-checking plugin
Install spell-checking plugin to avoid typos and mispellings. For VS code, check out [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker).

### Liskov substitution
Create superclass instead of subclass if superclass can literally replace subclass without missing features.
```tsx
// Good
const DateInput = () => {};

// Bad
const DOBInput = () => {};
const PolicyDateInput = () => {};
```

# Typescript

### How to define functional component with typescript
Define component props and state as type.
```tsx
// Declaring type of props - see "Typing Component Props" for more examples
type AppProps = {
  message: string;
};
const App = ({ message }: AppProps) => <div>{message}</div>;

// you can also inline the type declaration; eliminates naming the prop types, but looks repetitive
const App = ({ message }: { message: string }) => <div>{message}</div>;
```

### Useful types
```tsx
type AppProps = {
  message: string;
  count: number;
  disabled: boolean;
  /** array of a type! */
  names: string[];
  /** string literals to specify exact string values, with a union type to join them together */
  status: "waiting" | "success";
  /** any object as long as you dont use its properties (NOT COMMON but useful as placeholder) */
  obj: object;
  obj2: {}; // almost the same as `object`, exactly the same as `Object`
  /** an object with any number of properties (PREFERRED) */
  obj3: {
    id: string;
    title: string;
  };
  /** array of objects! (common) */
  objArr: {
    id: string;
    title: string;
  }[];
  /** a dict object with any number of properties of the same type */
  dict1: {
    [key: string]: MyTypeHere;
  };
  dict2: Record<string, MyTypeHere>; // equivalent to dict1
  /** any function as long as you don't invoke it (not recommended) */
  onSomething: Function;
  /** function that doesn't take or return anything (VERY COMMON) */
  onClick: () => void;
  /** function with named prop (VERY COMMON) */
  onChange: (id: number) => void;
  /** alternative function type syntax that takes an event (VERY COMMON) */
  onClick(event: React.MouseEvent<HTMLButtonElement>): void;
  /** an optional prop (VERY COMMON!) */
  optional?: OptionalType;
};
```

```tsx
export type AppProps = {
  children1: JSX.Element; // bad, doesnt account for arrays
  children2: JSX.Element | JSX.Element[]; // meh, doesn't accept strings
  children3: React.ReactChildren; // despite the name, not at all an appropriate type; it is a utility
  children4: React.ReactChild[]; // better, accepts array children
  children: React.ReactNode; // best, accepts everything (see edge case below)
  functionChildren: (name: string) => React.ReactNode; // recommended function as a child render prop type
  style?: React.CSSProperties; // to pass through style props
  onChange?: React.FormEventHandler<HTMLInputElement>; // form events! the generic parameter is the type of event.target
}
```   

# References
- [React+TypeScript Cheatsheets](https://github.com/typescript-cheatsheets/react)
- [Presentational and Container Components - Dan Abramov](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)
- [Google TypeScript Style Guide](https://google.github.io/styleguide/tsguide.html)
- [Typescript - Reference - Enums](https://www.typescriptlang.org/docs/handbook/enums.html)
