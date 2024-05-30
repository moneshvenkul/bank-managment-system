<template>
  <div class="LoginPageMainContainer">
    <div class="LoginPageSubContainer">
      <h1>Welcome Back!</h1>
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
      <div class="flexrow">
        <p>
          new User?
          <router-link to="/Register">Register A New Account</router-link>
        </p>
        <div class="LoginRegisterContainer">
          <ButtonComp clr="red" size="Small" text="Cancel" @click="Cancel" />
          <ButtonComp clr="blue" size="Small" text="Login" @click="Login" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ButtonComp from "./ButtonComp.vue";
import axios from "axios";
export default {
  name: "LoginPage",
  components: { ButtonComp },
  data() {
    return {
      Email: "",
      Password: "",
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
    Login() {
      const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      const rep =
        /^(?=.*[A-Za-z])(?=.*\d)(?=.*[@$!%*#?&])[A-Za-z\d@$!%*#?&]{8,}$/;
      if (re.test(this.Email)) {
        if (rep.test(this.Password)) {
          axios
            .get("http://localhost:8000/Login.php", {
              params: {
                email: this.Email,
                password: this.Password,
              },
            })
            .then((response) => {
              if (response.data.State == 1) {
                localStorage.setItem("UserID", response.data.id);
                localStorage.setItem("Email", this.Email);
                localStorage.setItem("Username", response.data.Username);
                localStorage.setItem("Amount", response.data.Amount);
                localStorage.setItem(
                  "Transactions",
                  JSON.stringify(response.data.Transactions)
                );
              } else if (response.data.State == 0) {
                alert("Wrong Password");
              }
            })
            .catch(function (error) {
              console.error("Error:", error);
            })
            .finally(() => {
              localStorage.setItem("Logged", 1);

              this.$router.push({ name: "Home" });
            });
        } else {
          alert("Password Is Invalid");
        }
      } else {
        alert("Email Is Invalid");
      }
    },
  },
};
</script>

<style>
.LoginPageMainContainer {
  width: 25%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
.LoginPageSubContainer {
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
