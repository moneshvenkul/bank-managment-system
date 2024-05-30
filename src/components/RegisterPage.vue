<template>
  <div class="RegisterPageMainContainer">
    <div class="RegisterPageSubContainer">
      <h1>Create A Free Account</h1>
      <div class="InputContainer">
        <input
          type="normal"
          class="InputArea"
          v-model="Username"
          placeholder="Username"
        />
      </div>
      <div class="InputContainer">
        <input
          type="email"
          class="InputArea"
          v-model="Email"
          placeholder="Email"
        />
      </div>
      <div class="InputContainer">
        <input
          type="password"
          class="InputArea"
          v-model="Password"
          placeholder="Password"
        />
      </div>
      <div class="InputContainer">
        <input
          type="password"
          class="InputArea"
          v-model="ConfirmPassword"
          placeholder="Confirm Password"
        />
      </div>
      <div class="flexrow">
        <p>
          Already Have An Account? <router-link to="/Login">Login</router-link>
        </p>
        <div class="LoginRegisterContainer">
          <ButtonComp clr="red" size="Small" text="Cancel" @click="Cancel" />
          <ButtonComp
            clr="blue"
            size="Small"
            text="Register"
            @click="Register"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ButtonComp from "./ButtonComp.vue";
import axios from "axios";

export default {
  name: "RegisterPage",
  components: { ButtonComp },
  data() {
    return {
      Username: "",
      Email: "",
      Password: "",
      ConfirmPassword: "",
    };
  },
  methods: {
    Cancel() {
      localStorage.setItem("Email", "");
      localStorage.setItem("Username", "Guest");
      localStorage.setItem("Amount", 0);
      localStorage.setItem("Transactions", JSON.stringify([]));
      localStorage.setItem("Logged", 0);

      this.$router.push({ name: "Home" });
    },
    Login(id) {
      localStorage.setItem("UserID", id);
      localStorage.setItem("Email", this.Email);
      localStorage.setItem("Username", this.Username);
      localStorage.setItem("Amount", 0);
      localStorage.setItem("Transactions", JSON.stringify([]));
      localStorage.setItem("Logged", 1);

      this.$router.push({ name: "Home" });
    },
    Register() {
      if (this.Username != "Guest") {
        const ren = /^[a-zA-Z]{3,}$/;
        const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
        const rep =
          /^(?=.*[A-Za-z])(?=.*\d)(?=.*[@$!%*#?&])[A-Za-z\d@$!%*#?&]{8,}$/;
        if (re.test(this.Email)) {
          if (
            rep.test(this.Password) &&
            this.Password === this.ConfirmPassword
          ) {
            if (ren.test(this.Username)) {
              axios
                .post("http://localhost:8000/Register.php", {
                  username: this.Username,
                  email: this.Email,
                  password: this.Password,
                })
                .then((response) => {
                  if (response.data.state == 1) {
                    this.Login(response.data.userid);
                  } else if (response.data.state == 2) {
                    alert("Account Exists Already");
                  }
                })
                .catch(function (error) {
                  console.error("Error:", error);
                });
            } else {
              alert("Username Is Invalid");
            }
          } else {
            alert("Password Is Invalid");
          }
        } else {
          alert("Email Is Invalid");
        }
      } else {
        alert("Cant Use This Name!");
      }
    },
  },
};
</script>

<style>
.RegisterPageMainContainer {
  width: 25%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.RegisterPageSubContainer {
  width: 100%;
  height: fit-content;
  padding: 25px 50px;
  border-radius: 10px;
  display: flex;
  justify-content: start;
  align-items: center;
  flex-direction: column;
  gap: 25px;
  background-color: #ffffff;
}
.flexrow {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
a:hover {
  transition: 0.25s;
  color: #447604;
}
</style>
