<template>
  <div class="ButtonContainer">
    <button :class="size + ' ' + clr" class="Button" @click="handleClick">
      {{ text }}
    </button>
  </div>
</template>

<script>
export default {
  name: "ButtonComp",
  props: {
    text: {
      type: String,
      required: true,
    },
    size: {
      type: String,
      required: true,
    },
    clr: {
      type: String,
      required: true,
    },
  },
  data() {
    return {};
  },
  methods: {
    handleClick() {
      this.$emit("click");
    },
  },
};
</script>

<style>
.ButtonContainer {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: fit-content;
  gap: 0px;
}
.Button {
  transition: 0.25s;
  border: none;
  font-size: 15px;
  border-radius: 2px;
  font-weight: bold;
  background-color: #fcfcfc;
  box-shadow: rgba(0, 0, 0, 0.4) 0px 2px 4px,
    rgba(0, 0, 0, 0.3) 0px 7px 13px -3px, rgba(0, 0, 0, 0.2) 0px -3px 0px inset;
}
.Big {
  padding: 15px 35px;
}
.Medium {
  padding: 10px 20px;
}
.Small {
  padding: 5px 15px;
}

.Button:hover {
  transition: 0.25s;
  cursor: pointer;
  color: #fcfcfc;
  border-radius: 5px;
}
.Button:hover.red {
  background-color: #d05353;
}
.Button:hover.blue {
  background-color: #1b065e;
}
.Button:hover.green {
  background-color: #447604;
}
</style>
