<template>
  <div class="container">
    <div class="row">

  <div class="px-3 mx-auto col-12 col-lg-6">
      <p class="mb-1">{{label}}</p>

      <!-- use directive v-click-outdide to hide the dropdown on out click -->
      <div 
        class="search-container w-100" 
        v-click-outside="hide"
      >
        <!-- Show query on dropdown open and show selected value or selectedvalues on dropdown close -->
        <input
            type="text"
            class="search-input w-100 rounded border border-primary"
            @click="showDropdown = true"
            @input="changeQuery"
            :placeholder="placeholder"
            :value="showDropdown ? query : (multiple ? allSelectedValues : (selectedValue !== null ? selectedValue.value : ''))"
        />
        <button type="submit" class="search-icon bg-white border border-white"><i class="fa fa-search"></i></button>
        <div
            class="search-dropdown bg-white border border-primary"
            id="searchDropdown"
            :class="{ show: showDropdown }"
        >
          <div 
            v-for="option in filteredOptions" :key="option.id"
            :class="[((multiple && selectedOptions[option.id]) || (selectedValue && option.id === selectedValue.id))  ? 'selected' : '', 'search-item p-2 border-bottom border-info']"
            @click="selectedOption(option)"
          >

          <!-- Show checkbox if multiple prop selected -->
              <input v-if="multiple" type="checkbox" class="check-input" :checked="selectedOptions[option.id]" />
              <span class="option-text">{{option.value}}</span>
          </div>

          <!-- Show no items found when no items found  -->
          <div 
            v-if="!filteredOptions.length"
            class="p-2"
          >
             No items Found
          </div>
      </div>
    </div>
  </div>
  </div>
  </div>
</template>
<script>
import ClickOutside from 'vue-click-outside'
export default {
  name: "CustomSelect",
  directives: {
      ClickOutside
  },
  props: {
    /**
     * Label prop to change label as per requirement
     */
      label: {
          type: String,
          default: "Custom Search"
      },
      /**
       * Option array to show all options
       */
      options: {
          type: Array,
          default: () => [],
      },
      /**
       * multiple prop to select multiple items
       */
      multiple: {
          type: Boolean,
          default: false,
      },
      /**
       * Customize placeholder prop
       */
      placeholder: {
          type: String,
          default: "Search..."
      },
      /**
       * Model value to update the selected values
       */
      modelValue: {
          type: String | Array,
          default: ""
      }
  },
  data() {
    return {
      showDropdown: false,
      query: "",
      selectedValue: null, // for single value 
      selectedOptions : {},  // for multiple values
      allSelectedValues: "", // display property for multiple values
    };
  },
  watch: {
    /**
     * Watcher on model value to change selected values if parent change model value from the top
     */
    modelValue: {
        handler(newValue) {
            if(this.multiple) {
                this.selectedOptions = {};
                newValue.forEach(element => {
                    this.selectedOptions[element.id] = element.value;
                });
                this.allSelectedValues =  Object.values(this.selectedOptions).join(", ");
            } else {
                this.selectedValue = newValue;
            }
        },
        // immediate true so it bind values in starting
        immediate: true
    }
  },
  methods: {
    /**
     * Method to hide the dropdown and empty the query
     */
      hide() {
          this.query = "";
          this.showDropdown = false;
      },
      /**
       * Method to perform operation on user select item
       * 
       * @param {Object} item - the item selected by the user
       */
      selectedOption(item) {
        // handle multiple and single item
          if(this.multiple) {

            // if user selects it first time then add otherwise remove on next click
              if(this.selectedOptions[item.id]) {
                  delete this.selectedOptions[item.id];
              }
              else {
                  this.selectedOptions[item.id] = item.value;
              }

              // update all values
              this.allSelectedValues =  Object.values(this.selectedOptions).join(", ");
              const selectedValuesArray = Object.entries(this.selectedOptions).map((val) => ({id: val[0], value: val[1]}));
              
              // emit the updated values
              this.$emit("update", selectedValuesArray);
          }
          else {
              this.selectedValue = item;
              this.$emit("update", this.selectedValue);
          }

          // refresh query and hide dropdown on click
          this.query = "";
          this.showDropdown = false;
      },
      /**
       * Method to change the query on input update
       * 
       * @param {Event} e - the event element 
       */
      changeQuery(e) {
          this.query = e.target.value;
      }
  },
  computed: {
    /**
     * Computed property to show filtered item on query update
     */
      filteredOptions() {
          return (this.query === ''
              ? this.options
              : this.options.filter((option) => {
                  return option.value.toLowerCase().includes(this.query.toLowerCase())
          }));
      },
  },
};
</script>
<style>

.search-container {
  position: relative;
  display: inline-block;
  margin-top: 0;
}

.search-input {
  padding: 5px 30px 5px 5px;
}

.search-icon {
    position: absolute;
    top: 50%;
    right: 5px;
    transform: translateY(-50%);
    cursor: pointer;
}

.search-dropdown {
  position: absolute;
  top: 100%;
  left: 0;
  z-index: 1;
  max-height: 150px;
  overflow-y: auto;
  display: none;
  width: 99%;
}

.search-dropdown.show {
  display: block;
}

.search-item {
  cursor: pointer;
}

.search-item:hover{
  background: rgb(226, 224, 224);
}
.check-input {
  text-align: start;
}
.selected {
  background: rgb(173, 233, 241);
  border: 2px solid rgb(150, 150, 249)
}
.selected:hover {
  background: rgb(92, 187, 230);
}
::-webkit-scrollbar {
  width: 5px;
}
/* Track */
::-webkit-scrollbar-track {
  background: #f1f1f1; 
  border-radius: 10%;
}

::-webkit-scrollbar-thumb {
  background: #888; 
  background-clip: padding-box; 
}
</style>