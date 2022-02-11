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
<!-- ❌ This will pass: -->
<script>
it("calculates double correctly", () => {
  createComponent({ data: { count: 1 } });

  expect(wrapper.vm.double).toBe(2);
});
</script>
<!-- ✅ This will not: -->
<script>
const findDouble = () => wrapper.find("[data-testid='double']");

it("calculates double correctly", () => {
  createComponent({ data: { count: 1 } });

  expect(findDouble().text()).toBe(2);
});
</script>
