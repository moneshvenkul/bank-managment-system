<template>
  <div class="BlackBackground">
    <div class="SendReqContainer">
      <h1 class="Title">{{ State }} Money</h1>
      <div class="InputContainer">
        <input class="InputArea" v-model="Username" placeholder="username" />
      </div>
      <div class="InputContainer">
        <input
          class="InputArea"
          v-model="Amount"
          :placeholder="State + ' Amount'"
          @input="handleChange"
        />
      </div>
      <div class="ButtonsContainer">
        <ButtonComp clr="red" size="Medium" text="Cancel" @click="Cancel" />
        <ButtonComp
          clr="green"
          size="Medium"
          text="Confirm"
          @click="SendRequest"
        />
      </div>
    </div>
  </div>
</template>

<script>
import ButtonComp from "./ButtonComp.vue";
export default {
  name: "SendRequest",
  components: { ButtonComp },
  props: {
    State: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      Username: "",
      Amount: "",
    };
  },
  methods: {
    Cancel() {
      this.$emit("Cancel");
    },
    SendRequest() {
      if (this.Username.trim() !== "" && this.Amount.trim() !== "") {
        this.$emit("Confirm", [this.Username, this.Amount]);
      } else {
        this.Cancel();
      }
    },
    handleChange() {
      if (
        !(this.Amount.substring(-1) == "." || !isNaN(this.Amount.substring(-1)))
      ) {
        this.Amount = this.Amount.slice(0, -1);
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
  top: 0%;
  left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  background-color: rgba(0, 0, 0, 0.451);
  width: 100%;
  height: 100%;
}
.SendReqContainer {
  border-radius: 5px;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  background-color: #e8eef2;
  gap: 25px;
  width: 300px;
  height: 40%;
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
