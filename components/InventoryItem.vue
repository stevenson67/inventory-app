<template>
  <div
    :class="['inventory__item', { empty: !item }]"
    @dragover.prevent="allowDrop"
    @drop.prevent="onDrop"
    @click="selectItem"
  >
    <div v-if="item" class="item" draggable="true" @dragstart="onDragStart">
      <div class="square">
        <span :style="{ 'background-color': item.color }"></span>
        <span :style="{ 'background-color': item.color + '35' }"></span>
      </div>
      <span class="quantity">{{ item.quantity }}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "InventoryItem",
  props: {
    item: Object,
    index: Number
  },
  methods: {
    // Method to handle drag start event
    onDragStart(event) {
      this.$emit('dragstart', event, this.index);
    },
    // Method to allow dropping of items
    allowDrop(event) {
      event.preventDefault();
    },
    // Method to handle drop event
    onDrop(event) {
      this.$emit('drop', this.index);
    },
    // Method to select an item
    selectItem() {
      this.$emit('select', this.index);
    }
  }
}
</script>

<style>
.item {
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  width: 100%;
  height: 100%;
}
.quantity {
  position: absolute;
  right: 0;
  bottom: 0;
  color: #7d7d7d;
  padding: 2px 4px;
  border-top: 1px solid #4D4D4D;
  border-left: 1px solid #4D4D4D;
  border-radius: 6px 0 0 0;
}
</style>
