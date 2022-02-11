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
<script>
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
</script>
