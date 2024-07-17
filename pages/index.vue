<template>
  <div>
    <div class="wrap">
      <div class="interface">
        <div class="interface__img">
          <img src="../static/img/img-blur.png" alt="Img Blur">
        </div>
        <div class="interface__info">
          <div class="interface__text">
            <span class="gradient"></span>
            <span class="gradient"></span>
            <span class="gradient"></span>
            <span class="gradient"></span>
            <span class="gradient"></span>
            <span class="gradient"></span>
            <span class="gradient"></span>
          </div>
          <button class="interface__button gradient" @click="addItem">Добавить предмет</button>
        </div>
      </div>
      <div class="inventory">
        <div class="inventory__wrap">
          <InventoryItem
            v-for="(item, index) in paddedInventory"
            :key="index"
            :item="item"
            :index="index"
            @dragstart="onDragStart"
            @drop="onDrop"
            @select="selectItem"
          />
        </div>
        <transition name="modal">
          <EditItemModal
            v-if="isEditModalVisible"
            :item="selectedItem"
            @close="hideEditModal"
            @remove-item="removeItem"
          />
        </transition>
      </div>
    </div>
    <div class="down">
      <div class="close">
        <img src="../static/img/close.svg" alt="Close">
      </div>
      <span class="gradient"></span>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexPage',
  data() {
    return {
      inventory: Array(25).fill(null),
      draggedItem: null,
      draggedIndex: null,
      isEditModalVisible: false,
      selectedItem: null,
      selectedIndex: null
    };
  },
  computed: {
    // Возвращает массив инвентаря, 25 ячеек с нулевыми значениями
    paddedInventory() {
      const totalSlots = 25;
      const padded = this.inventory.slice(0, totalSlots);
      while (padded.length < totalSlots) {
        padded.push(null);
      }
      return padded;
    }
  },
  mounted() {
    this.loadInventory();
  },
  methods: {
    // Загружает данные из localStorage
    loadInventory() {
      const savedInventory = JSON.parse(localStorage.getItem('inventory')) || [];
      this.inventory = savedInventory;
    },
    // Сохраняет localStorage
    saveInventory() {
      localStorage.setItem('inventory', JSON.stringify(this.inventory));
    },
    // Добавление нового товара в инвентарь
    addItem() {
      const newItem = {
        color: '#' + Math.floor(Math.random() * 16777215).toString(16),
        quantity: Math.floor(Math.random() * 10) + 1
      };
      const emptySlotIndex = this.inventory.findIndex(i => i == null);
      if (emptySlotIndex !== -1) {
        this.$set(this.inventory, emptySlotIndex, newItem);
      } else {
        this.inventory.push(newItem);
      }
      this.saveInventory();
    },
    // Редактирование товара
    selectItem(index) {
      if (this.inventory[index]) {
        this.selectedIndex = index;
        this.selectedItem = this.inventory[index];
        this.isEditModalVisible = true;
      }
    },
    // Скрыть редактирование товара
    hideEditModal() {
      this.isEditModalVisible = false;
    },
    // Удалить указанное количество из выбранного товара
    removeItem(amount) {
      if (this.selectedItem.quantity > amount) {
        this.selectedItem.quantity -= amount;
      } else {
        this.$set(this.inventory, this.selectedIndex, null);
      }
      this.saveInventory();
      this.hideEditModal();
    },
    // Обрабатывание начало события перетаскивания
    onDragStart(event, index) {
      this.draggedItem = this.inventory[index];
      this.draggedIndex = index;
      event.dataTransfer.effectAllowed = 'move';
    },
    // Обрабатывание событие выпадения и замена местами товары в инвентаре
    onDrop(index) {
      if (this.draggedItem !== null) {
        const targetItem = this.inventory[index];
        this.$set(this.inventory, index, this.draggedItem);
        this.$set(this.inventory, this.draggedIndex, targetItem);
        this.draggedItem = null;
        this.draggedIndex = null;
        this.saveInventory();
      }
    }
  }
}
</script>

<style>
body {
  background: #1D1D1D;
  padding: 32px;
}
.wrap {
  position: relative;
  overflow: hidden;
  display: flex;
  gap: 24px;
  margin-bottom: 24px;
}
.interface {
  flex-grow: 2;
  padding: 18px 14px;
  background: #262626;
  border: 1px solid #4D4D4D;
  border-radius: 12px;
}
.interface__img img {
  width: 100%;
  height: 240px;
  object-fit: cover;
  border-radius: 8px;
  margin-bottom: 20px;
}
.interface__info {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.interface__text {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 80%;
}
.interface__text span:nth-child(1) {
  width: 100%;
  height: 26px;
  border-radius: 8px;
  margin-bottom: 24px;
}
.interface__text span:nth-child(2),
.interface__text span:nth-child(3),
.interface__text span:nth-child(4),
.interface__text span:nth-child(5),
.interface__text span:nth-child(6),
.interface__text span:nth-child(7) {
  height: 10px;
  border-radius: 4px;
  margin-bottom: 16px;
}
.interface__text span:nth-child(2) {
  width: 80%;
}
.interface__text span:nth-child(3) {
  width: 100%;
}
.interface__text span:nth-child(4) {
  width: 90%;
}
.interface__text span:nth-child(5) {
  width: 70%;
}
.interface__text span:nth-child(6) {
  width: 60%;
  margin-bottom: 24px;
}
.interface__text span:nth-child(7) {
  width: 50%;
}
.interface__button {
  border-radius: 8px;
  height: 26px;
  padding: 6px 18px;
  color: #e0e0e0;
}
.inventory {
  position: relative;
  overflow: hidden;
  flex-grow: 10;
  background: #262626;
  border-radius: 12px;
  border: 1px solid #4D4D4D;
}
.inventory__wrap {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-template-rows: repeat(5, 1fr);
  height: 100%;
}
.down {
  position: relative;
  background: #262626;
  border-radius: 12px;
  border: 1px solid #4D4D4D;
  padding: 18px;
}
.down .close {
  position: absolute;
  top: 0; right: 0;
}
.down .gradient {
  height: 36px;
  width: 90%;
  border-radius: 12px;
}
.inventory__item {
  position: relative;
  border: .5px solid #4D4D4D;
  height: 100%;
}
.inventory__item.empty {
  background: #2b2b2b;
}
.item {
  cursor: grab;
  background: #2F2F2F;
  border: 0.5px solid #4D4D4D;
}
.modal-enter, .modal-leave-to {
  transform: translateX(100%);
}
</style>
