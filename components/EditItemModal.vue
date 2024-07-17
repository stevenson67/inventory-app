<template>
  <div class="modal">
    <div class="modal-content">
      <div class="close" @click="$emit('close')">
        <img src="../static/img/close.svg" alt="Close">
      </div>
      <div class="square">
        <span :style="{ backgroundColor: item.color }"></span>
        <span :style="{ backgroundColor: rgbaColor(item.color, 0.35) }"></span>
      </div>
      <div class="modal-info">
        <span class="gradient"></span>
        <span class="gradient"></span>
        <span class="gradient"></span>
        <span class="gradient"></span>
        <span class="gradient"></span>
        <span class="gradient"></span>
        <span class="gradient"></span>
      </div>
      <div class="modal-actions">
        <input v-model.number="amountToRemove" type="number" placeholder="Введите количество"/>
        <div class="modal-btn">
          <button class="cancel" @click="$emit('close')">Отмена</button>
          <button class="accept" @click="confirmRemove">Подтвердить</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "EditItemModal",
  props: {
    item: Object
  },
  data() {
    return {
      amountToRemove: ''
    };
  },
  methods: {
    hexToRgb(hex) {
      if (!/^#([A-Fa-f0-9]{3}){1,2}$/.test(hex)) {
        return null;
      }
      const shorthandRegex = /^#?([a-f\d])([a-f\d])([a-f\d])$/i;
      hex = hex.replace(shorthandRegex, (m, r, g, b) => {
        return r + r + g + g + b + b;
      });

      const result = /^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
      return result ? {
        r: parseInt(result[1], 16),
        g: parseInt(result[2], 16),
        b: parseInt(result[3], 16)
      } : null;
    },
    rgbaColor(hex, alpha) {
      const rgb = this.hexToRgb(hex);
      if (rgb) {
        return `rgba(${rgb.r}, ${rgb.g}, ${rgb.b}, ${alpha})`;
      } else {
        // Цвет по умолчанию, если переданный цвет некорректный
        return `rgba(0, 0, 0, ${alpha})`;
      }
    },
    confirmRemove() {
      if (this.amountToRemove > 0 && this.amountToRemove <= this.item.quantity) {
        this.$emit('remove-item', this.amountToRemove);
      }
    }
  }
}
</script>

<style>
.modal {
  position: absolute;
  top: 0;
  right: 0;
  height: 100%;
  border-left: 1px solid #4D4D4D;
  z-index: 99999999;
  transform: translateX(0);
  transition: transform 0.3s ease-out, opacity 0.3s ease;
}
.modal:before {
  content: '';
  position: absolute;
  width: 100%;
  height: 100%;
  background: rgb(38 38 38 / 70%);
  backdrop-filter: blur(10px);
  z-index: 0;
}
.close {
  display: flex;
  justify-content: flex-end;
  padding-top: 14px;
  padding-right: 14px;
}
.close img {
  width: 12px; height: 12px;
}
.modal-content {
  position: relative;
  height: 100%;
  z-index: 1;
}
.modal-content .square {
  width: 130px;
  height: 130px;
  margin: 30px 45px;
}
.modal-info {
  margin: 20px;
  padding-top: 16px;
  border-top: 1px solid #4D4D4D;
}
.modal-info span:nth-child(1) {
  width: 100%;
  height: 30px;
  border-radius: 8px;
  margin-bottom: 24px;
}
.modal-info span:nth-child(2),
.modal-info span:nth-child(3),
.modal-info span:nth-child(4),
.modal-info span:nth-child(5),
.modal-info span:nth-child(6),
.modal-info span:nth-child(7) {
  height: 10px;
  border-radius: 4px;
  margin-bottom: 16px;
}
.modal-actions {
  position: absolute;
  left: 0;
  bottom: 0;
  width: -webkit-fill-available;
  display: flex;
  flex-direction: column;
  gap: 20px;
  padding: 20px;
  border-top: 1px solid #4D4D4D;
  background: rgba(38, 38, 38, 1);
  z-index: 999999;
}
.modal-actions input {
  font-size: 14px;
  border: 1px solid #4D4D4D;
  border-radius: 4px;
  padding: 12px;
  background: transparent;
  color: #ffffff;
}
.modal-btn {
  display: flex;
  gap: 10px;
}
.modal-btn button {
  box-shadow: 0 0 20px 0 #FA7272;
}
.modal-btn .cancel {
  background: #FFFFFF;
}
.modal-btn .accept {
  background: #FA7272;
  color: #FFFFFF;
}
</style>
