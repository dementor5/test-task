<template>
  <div class="container">
    <input
      ref="searchField"
      v-model.trim="question"
      class="searchField"
      type="text"
      @focus="showDropdown"
      @blur="startHiding"
      @keydown.up.prevent="onUpArrow"
      @keydown.down.prevent="onDownArrow"
      @keydown.enter.prevent="onEnter"
      @keydown.esc="startHiding"
      @input="showDropdown"
    >
    <ul
      v-if="isDroppedDown && filteredItems.length"
      class="it-list"
    >
      <li
        v-for="(it, i) in filteredItems"
        :key="it.value"
        class="it"
        :class="{'it--hover': i === hoveredIndex}"
        @mouseenter="hoveredIndex = i"
        @mousedown="cancelHiding"
        @mouseup="focuseSearchField"
      >
        <label class="item__label">
          <input
            type="checkbox"
            tabindex="-1"
            :checked="isSelected(it)"
            @change="onEnter"
          >{{ it.label }}
        </label>
      </li>
    </ul>
    <ul
      v-else-if="isDroppedDown"
      class="it-list"
    >
      <li class="it">
        Совпадений нет
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'MultiSelect',
  model: {
    event: 'change',
  },
  props: {
    items: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      isDroppedDown: false,
      hideTimerId: null,
      hoveredIndex: 0,
      question: '',
      filteredItems: [],
      selectedItems: [],
    };
  },
  computed: {
    lastIndex() {
      return this.filteredItems.length ? this.filteredItems.length - 1 : 0;
    },
    selectedValues() {
      return this.selectedItems.map(it => it.value);
    },
  },
  watch: {
    question(question) {
      const lastHoveredItem = this.filteredItems[this.hoveredIndex];
      this.filteredItems = !question ? this.items
        : this.items.filter(it => it.label.toLowerCase().indexOf(question.toLowerCase()) > -1);
      this.setNewHoveredIndex(lastHoveredItem);
    },
  },
  created() {
    this.filteredItems = this.items;
  },
  methods: {
    showDropdown() {
      this.isDroppedDown = true;
    },
    startHiding() {
      this.hideTimerId = setTimeout(() => {
        this.isDroppedDown = false;
      });
    },
    cancelHiding() {
      setTimeout(() => {
        clearTimeout(this.hideTimerId);
      });
    },
    focuseSearchField() {
      setTimeout(() => {
        this.$refs.searchField.focus();
      });
    },


    isSelected(item) {
      const index = this.selectedItems.findIndex(it => it.value === item.value);
      return index > -1;
    },


    setNewHoveredIndex(lastHoveredItem) {
      const lastHoveredValue = lastHoveredItem ? lastHoveredItem.value : '';
      if (lastHoveredValue) {
        const newIndex = this.filteredItems.findIndex(it => it.value === lastHoveredValue);
        if (newIndex > -1) {
          this.hoveredIndex = newIndex;
          return;
        }
      }
      this.hoveredIndex = this.hoveredIndex > this.lastIndex ? this.lastIndex : 0;
    },


    onUpArrow() {
      if (!this.isDroppedDown) {
        return;
      }
      if (this.hoveredIndex > 0) {
        this.hoveredIndex -= 1;
      } else {
        this.hoveredIndex = this.lastIndex;
      }
    },
    onDownArrow() {
      if (!this.isDroppedDown) {
        return;
      }
      if (this.hoveredIndex < this.lastIndex) {
        this.hoveredIndex += 1;
      } else {
        this.hoveredIndex = 0;
      }
    },
    onEnter() {
      if (!this.filteredItems.length || !this.isDroppedDown) {
        return;
      }
      const lastHoveredItem = this.filteredItems[this.hoveredIndex];
      if (!lastHoveredItem) {
        return;
      }

      const index = this.selectedItems.findIndex(it => it.value === lastHoveredItem.value);
      if (index === -1) {
        this.selectedItems.push(lastHoveredItem);
      } else {
        this.selectedItems.splice(index, 1);
      }
      this.$emit('change', this.selectedValues);
    },
  },
};
</script>

<style lang="scss">
.container {
  $variable: 1px;
  box-sizing: border-box;
  width: 250px;
  margin: 0 auto;
}

*,
::before,
::after {
  box-sizing: inherit;
}

.searchField {
  width: 100%;
  margin-bottom: 1px;
}

.it-list {
  margin: 0;
  padding: 0;

  list-style: none;

  border: 1px solid red;
}

.it {
  &:not(:first-child) {
    border-top: 1px solid blue;
  }

  &--hover {
    background-color: yellow;
  }
}

.item__label {
  display: block;
}
</style>
