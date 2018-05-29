# Mapping concepts between Vue and React

## Constant string props
Communicating parent -> child

### Vue

```js
const Child = {
  props: ['text'],
  template: '<p>{{ text }}</p>'
};

const Parent = {
  components: {
    Child,
  },
  template: '<child text="Some text"></child>'
};
```

### React
```js
const Child = ({ text }) => <p>{text}</p>;
const Parent = () => <Child text="Some text" />;
```

## Dynamic, JS primitive props
Communicating parent -> child

```js
const object = {
  string: "This has a value",
  number: 44
};
```

### Vue
```js
const Child = {
  props: ['object'],
  template: '<p>{{ object.string }}</p>'
};

const Parent = {
  components: {
    Child,
  },
  data() {
    return {
      object,
    }
  },
  template: '<child :object="object"></child>'
};
```

### React
```js
const Child = ({ object }) => <p>{object.text}</p>;
const Parent = () => <Child object={object} />;
```

## Events (Vue) / Functions (React)
Communicating from child -> parent

### Vue
```js
const MyButton = {
  props: ['text'],
  template: '<button @click="handleClick">{{ text }}</button>',
  methods: {
    handleClick(e) {
      e.preventDefault();
      this.$emit('click');
    }
  }
};

const Parent = {
  components: {
    MyButton,
  },
  template: '<my-button @click="handleClick" text="Click me!"></my-button>',
  methods: {
    handleClick() {
      alert('The button was clicked!');
    }
  }
};
```

### React
```js
const MyButton = ({ text, handleClick }) => (
  <button onClick={e => {
    e.preventDefault();
    handleClick();
  }}>
    {text}
  </button>
);

const Parent = () =>
  <MyButton
    handleClick={() => alert('The button was clicked!')}
    text="Click me!"
  />;
```

## Slots (with default content)
This example renders `<p>This is the text you gave me: Hello World</p>`

### Vue
```js
const TextWrapper = {
  props: ['text'],
  template: `
    <p>
      This is the text you gave me:
      <slot>
        Default content
      </slot>
    </p>
  `
};

const Parent = {
  components: { TextWrapper },
  template: '<text-wrapper>Hello World</text-wrapper>'
};
```

### React
```js
const TextWrapper = ({ children }) => (
  <p>
    This is the text you gave me:
    {children || "Default content"}
  </p>
);
const Parent = () => <TextWrapper>Hello World</TextWrapper>;
```

## Named slots
### Vue
```js
const WebPage = {
  template: `
    <div class="container">
      <header>
        <slot name="header"></slot>
      </header>
      <main>
        <slot></slot>
      </main>
      <footer>
        <slot name="footer"></slot>
      </footer>
    </div>
  `
};

const Parent = {
  components: {
    WebPage
  },
  template: `
    <web-page>
      <h1 slot="header">This is my cool website</h1>

      <p>This is the main content of the web page.</p>
      <p>So cool.</p>

      <address slot="footer">256 Main Street</address>
    </web-page>
  `
};
```

### React
```js
const WebPage = ({ header, footer, children }) => (
  <div className="container">
    {header}
    {children}
    {footer}
  </div>
);

const Parent = () =>
  <WebPage
    header={<h1>This is my cool website</h1>}
    footer={<address>256 Main Street</address>}
  >
    <p>This is the main content of the web page.</p>
    <p>So cool.</p>
  </WebPage>;
```

## Scoped slots
### Vue
```js
const TodoList = {
  template: `
    <ul>
      <li
        v-for="todo in todos"
        v-bind:key="todo.id"
      >
        <slot v-bind:todo="todo"></slot>
      </li>
    </ul>
  `
};

const Parent = {
  components: { TodoList },
  template: `
    <todo-list v-bind:todos="todos">
      <template slot-scope="slotProps">
        <span v-if="slotProps.todo.isComplete">✓</span>
        {{ slotProps.todo.text }}
      </template>
    </todo-list>
  `
};
```

### React: Render prop
Render prop can also be passed in as a child (often referred to as function as child component)
```js
const TodoList = ({ todos, render }) => (
  <ul>
    {
      todos.map(todo =>
        <li key={todo.id}>
          {render && render(todo)}
        </li>
      )
    }
  </ul>
);

const Parent = ({ todos }) => (
  <TodoList
    todos={todos}
    render={todo =>
      <React.Fragment>
        {todo.isComplete && <span>✓</span>}
        {todo.text}
      </React.Fragment>
    }
  />
);
```
