<template>
  <div class="BlackBackground">
    <div class="Container">
      <h1 class="Title">{{ title }} Money</h1>
      <div class="InputContainer">
        <input
          class="InputArea"
          v-model="data"
          :placeholder="'Amount To ' + title"
          @input="handleChange"
        />
      </div>
      <div class="ButtonsContainer">
        <ButtonComp clr="red" size="Medium" text="Cancel" @click="Cancel" />
        <ButtonComp clr="green" size="Medium" text="Confirm" @click="Deposit" />
      </div>
    </div>
  </div>
</template>

<script>
import ButtonComp from "./ButtonComp.vue";
export default {
  name: "DepositWithdrawComp",
  components: { ButtonComp },
  props: {
    title: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      data: "",
    };
  },
  methods: {
    Cancel() {
      this.$emit("Cancel");
    },
    Deposit() {
      if (this.data.trim() !== "") {
        this.$emit("confirm", this.data);
      } else {
        this.Cancel();
      }
    },
    handleChange() {
      if (
        !(this.data.substring(-1) == "." || !isNaN(this.data.substring(-1)))
      ) {
        this.data = this.data.slice(0, -1);
      }
    },
  },
};
</script>

<style>
.InputContainer {
  display: flex;
  justify-content: center;
  align-content: center;
  width: 100%;
  height: 10%;
}
.BlackBackground {
  position: absolute;
  top: 0;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  background-color: rgba(0, 0, 0, 0.451);
  width: 100%;
  height: 100%;
}
.Container {
  border-radius: 5px;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  background-color: #e8eef2;
  gap: 25px;
  width: 300px;
  height: 30%;
  box-shadow: rgba(0, 0, 0, 0.4) 0px 2px 4px,
    rgba(0, 0, 0, 0.3) 0px 7px 13px -3px, rgba(0, 0, 0, 0.2) 0px -3px 0px inset;
}
.ButtonsContainer {
  width: 260px;
  display: flex;
  justify-content: center;
  align-items: start;
  height: fit-content;
  gap: 60px;
}
.InputArea {
  width: 250px;
  height: 25px;
  font-size: 15px;
  margin: 0;
  padding: 0;
  border: none;
  box-shadow: rgba(0, 0, 0, 0.02) 0px 1px 3px 0px,
    rgba(27, 31, 35, 0.15) 0px 0px 0px 1px;
  border-radius: 2px;
  padding-left: 5px;
  background-color: #e8eef2;
  outline: none;
}
</style>
