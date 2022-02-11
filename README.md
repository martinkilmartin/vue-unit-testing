# 7 ways to make better Vue unit tests

[Source 🏹](https://www.youtube.com/watch?v=L_BK8zXGdBM)

## 1. Understand what to test and structure your test accordingly

### Test Component Output

- Rendered Template
- Emitted Events
- Side Effects (Vuex actions, routing, calling imported functions etc.)

❌ Not like this:

```js
desrcibe("methods", () => {
  /* Testing every method in isolation */
});
desrcibe("computed", () => {
  /* Testing every computed property in isolation */
});
desrcibe("template", () => {
  /* Testing what is rendered. With the shapshot 🧐*/
});
```

✅ More like this:

```html
<template>
  <main>
    <div v-if="loading">Loading...</div>
    <template v-else>
      <div v-if="error">Oops! 🤷</div>
      <div v-else>
        <!-- DATA -->
      </div>
    </template>
  </main>
</template>
```

```js
describe("when loading", () => {
  it.todo("renders `Loading...` text");
  it.todo("does not render error message");
  it.todo("does not render data");
});

describe("when there is an error", () => {
  it.todo("does not render `Loading...` text");
  it.todo("renders error message");
  it.todo("does not render data");
});
```

## 2. Start With A Component Factory

```js
import { shallowMount } from "@vue/test-utils";

describe("My component test", () => {
  let wrapper;

  // component factory
  function createComponent() {
    wrapper = shallowMount(MyComponent, {
      /* optional parameters */
    });
  }

  // destroying a wrapper
  afterEach(() => {
    wrapper.destroy();
  });
});
```

## 3. Use Helpers to Find Elements and Components

```js
import { shallowMount } from "@vue/test-utils";

describe("My component test", () => {
  let wrapper;

  const findConfirmButton = () =>
    wrapper.find('[data-testid="confirm-button"]');
  const findModal = () => wrapper.findComponent(GlModal);

  /* createComponent factory here */

  it("renders a modal", () => {
    createComponent();

    expect(findModal().exists()).toBe(true);
  });
});
```

#### Why?

```html
<template>
  <div>
    <button
      :disabled="title.length === 0"
      data-testid="create-button"
      class="confirm"
      @click="createWorkItem"
    >
      {{ __('Create') }}
    </button>
  </div>
</template>
```

Refactored to use componenet button:

```html
<template>
  <div>
    <gl-button
      :disabled="title.length === 0"
      variant="confirm"
      @click="createWorkItem"
    >
      {{ __('Create') }}
    </gl-button>
  </div>
</template>
```

## 4. Do Not Test Component Internals

```js
export default {
  data() {
    return {
      count: 0,
    };
  },
  computed: {
    double() {
      return this.count * 2;
    },
  },
  methods: {
    incrementCount() {
      this.count++;
    },
  },
};
```

```html
<template>
  <div>
    <span data-testid="count"> Count: {{ count }} </span>
    <button data-testid="increment-button" @click="incrementCount">
      Increment
    </button>
    <p data-testid="double">Count x2: {{ double }}</p>
  </div>
</template>
```

❌ Not like this:

```js
const findIncrementButton = () =>
  wrapper.find("[data-testid='increment-button']");

it("calculates double correctly", () => {
  createComponent({ data: { count: 1 } });

  expect(wrapper.vm.double).toBe(2);
});

it("calls correct method on button click", () => {
  createComponent();
  jest.spyOn(wrapper.vm, "incrementCount").mockImplementation(() => {});

  findIncrementButton().trigger("click");

  expect(wrapper.vm.incrementCount).toHaveBeenCalled();
  expect(wrapper.vm.count).toBe(1);
});
```

✅ More like this:

```js
const findDouble = () => wrapper.find("[data-testid='double']");

it("calculates double correctly", () => {
  createComponent({ data: { count: 1 } });

  expect(findDouble().text()).toBe(`Count x2: 2`);
});

// or for extended version

it.each`
  a      | expected
  ${0}   | ${0}
  ${1}   | ${2}
  ${101} | ${202}
`("renders double count as $expected when count is $a ", ({ a, expected }) => {
  createComponent({ data: { count: a } });
  expect(findDouble().text()).toBe(`Count x2: ${expected}`);
});
```

```js
const findCount = () => wrapper.find("[data-testid='count']");
const findIncrementButton = () =>
  wrapper.find("[data-testid='increment-button']");

it("increases the count on Increment button click", async () => {
  createComponent();
  expect(findCount().text()).toBe("Count: 0");

  findIncrementButton().trigger("click");
  await nextTick();

  expect(findCount().text()).toBe("Count: 1");
});
```

#### 👍 Rules of Thumb

- Forget about asserting `wrapper.vm`
  - `wrapper.vm` tests component internals ❌, not component output ✅
  - exceptions: `wrapper.$emit` and `nextTick()`
- Do not spy on methods
  - Check result or rendered template
  - Find side-effects
  - Mock imported functions and check if called
  - Check asynchronous requests
- If we rename method or computed, test should pass
  - Test knows nothing of component internals

#### Why?

```html
<template>
  <div>
    <span data-testid="count">Count: {{ count }}</span>
    <button data-testid="increment-button" @click="incrementCount">
      Increment
    </button>
    <!-- 🐞 `{{ count }}` should be `{{ double }}` -->
    <p data-testid="double">Count x2: {{ count }}</p>
  </div>
</template>
```

❌ This will pass:

```js
it("calculates double correctly", () => {
  createComponent({ data: { count: 1 } });

  expect(wrapper.vm.double).toBe(2);
});
```

✅ This will not:

```js
const findDouble = () => wrapper.find("[data-testid='double']");

it("calculates double correctly", () => {
  createComponent({ data: { count: 1 } });

  expect(findDouble().text()).toBe(2);
});
```

## 5. Follow The User

```html
<template>
  <div>
    <span data-testid="count">Count: {{ count }}</span>
    <button data-testid="increment-button" @click="incrementCount">
      Increment
    </button>
    <p data-testid="double">Count x2: {{ double }}</p>
  </div>
</template>
```

```js
it("increases double on increasing count", () => {
  createComponent({ data: { count: 1 } });
  expect(findDouble().text()).toBe(`Count x2: 2`);

  // ❌ Directly change component property
  // ❌ wrapper.vm.count = 2;
  // ❌ Directly attach data
  // ❌ wrapper.setData({ count: 2 });
  // ✅ Follow The User
  findIncrementButton().trigger("click");
  expect(findDouble().text()).toBe(`Count x2: 4`);
});
```

## 6. Do Not Change `wrapper.vm` directly

👍 The less `wrapper.vm` in the test, the better.

## 7. Child Components Are Black Boxes

```html
<template>
  <div>
    <MyButton :test="double" @click="onChildButtonClick">
      Child Component Button
    </MyButton>
    <span data-testid="child-counter">{{ childCounter }}</span>
  </div>
</template>

<script>
  import MyButton from "./MyButton.vue";
  export default {
    name: "MyComponent",
    components: {
      MyButton,
    },
    data() {
      return {
        childCounter: 1,
      };
    },
    computed: {
      double() {
        return this.childCounter * 2;
      },
    },
    methods: {
      onChildButtonClick() {
        this.childCounter++;
      },
    },
  };
</script>
```

```js
const findChildButton = () => wrapper.findComponent(MyButton);
const findChildCounter = () => wrapper.find("[data-testid='child-counter']");

it("passes a correct prop to child button", () => {
  createComponent({ data: { count: 5 } });

  expect(findChildButton().props("test")).toBe(10);
});

it("updated the child counter on child button `click` event", async () => {
  createComponent();
  expect(findChildCounter().text()).toBe("1");

  findChildButton.vm.$emit("click");
  await nextTick();
  expect(findChildCounter().text()).toBe("2");
});
```
