<template>
  <div class="main bg-light">
    <CustomSelect
      :modelValue="selectedValues"
      label="Custom Search with multiple prop"
      :options="options"
      :multiple="true"
      @update="(e) => selectedValues = e"
    />
  </div>
</template>

<script>
import CustomSelect from "@/components/CustomSelect";
export default {
  components: {
    CustomSelect,
  },
  data() {
    return{
      options: [
        {id: 1, value: "Python"},
        {id: 2, value: "Java"},
        {id: 3, value: "JS"},
        {id: 4, value: "HTML"},
        {id: 5, value: "C++"},
        {id: 6, value: "GO Lang"}
      ],
      selectedValues: [{id:1 , value: "Python"}],
    }
  }
};
</script>

<style>
.main {
  min-width: 100vw;
  min-height: 100vh;
}
</style>