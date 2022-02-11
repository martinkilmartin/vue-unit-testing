<script>
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
</script>