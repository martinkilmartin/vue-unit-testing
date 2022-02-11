<script>
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
</script>
<template>
  <div>
    <button
      :disabled="title.length === 0"
      data-testid="create-button"
      class="confirm"
      @click="createWorkItem"
    >
      {{ __("Create") }}
    </button>

    <!-- Refactored to use componenet button: -->

    <gl-button
      :disabled="title.length === 0"
      variant="confirm"
      @click="createWorkItem"
    >
      {{ __("Create") }}
    </gl-button>
  </div>
</template>