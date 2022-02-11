<script>
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
</script>
<template>
  <div>
    <span data-testid="count"> Count: {{ count }} </span>
    <button data-testid="increment-button" @click="incrementCount">
      Increment
    </button>
    <p data-testid="double">Count x2: {{ double }}</p>
  </div>
</template>
<!-- ❌ Not like this: -->
<script>
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
</script>
<!-- ✅ More like this: -->
<script>
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
</script>
<script>
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
</script>



