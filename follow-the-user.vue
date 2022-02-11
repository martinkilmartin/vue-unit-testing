<template>
  <div>
    <span data-testid="count">Count: {{ count }}</span>
    <button data-testid="increment-button" @click="incrementCount">
      Increment
    </button>
    <p data-testid="double">Count x2: {{ double }}</p>
  </div>
</template>
<script>
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
</script>
