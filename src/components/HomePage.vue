<template>
  <div class="MainContainer">
    <div class="MainHeader">
      <h1 class="WelcomeText">Welcome to CapitalOne - {{ username }}</h1>
      <div class="LoginRegisterContainer">
        <ButtonComp
          v-if="logged"
          clr="red"
          size="Small"
          text="Logout"
          @click="Logout"
        />
        <ButtonComp
          v-if="!logged"
          clr="blue"
          size="Small"
          text="Login"
          @click="ChangeRoute('Login')"
        />
        <ButtonComp
          v-if="!logged"
          clr="blue"
          size="Small"
          text="Register"
          @click="ChangeRoute('Register')"
        />
      </div>
    </div>
    <hr class="headerDivider" />
    <div class="headerContainer">
      <h1 class="Balance">{{ Balance }} USD</h1>
      <div class="MultiButtonContainer">
        <ButtonComp
          clr="blue"
          size="Big"
          text="Deposit Money"
          @click="Deposit"
        />
        <ButtonComp
          clr="blue"
          size="Big"
          text="Withdraw Money"
          @click="Withdraw"
        />
        <ButtonComp clr="blue" size="Big" text="Send Money" @click="Send" />
        <ButtonComp
          clr="blue"
          size="Big"
          text="Request Money"
          @click="Request"
        />
      </div>
    </div>
    <div class="TransactionsMainContainer" v-if="Transactions.length > 0">
      <TransactionComp
        v-for="(Transaction, index) in Transactions"
        :key="index"
        :ID="Transaction[0]"
        :Title="Transaction[1]"
        :Name="Transaction[2]"
        :Amount="Transaction[3]"
        :Date="Transaction[4]"
        :Color="Transaction[5]"
        :SendState="Transaction[6]"
        @send="SendToRequest($event)"
      />
    </div>
  </div>
  <DepositWithdrawComp
    v-if="DepositShow"
    @Cancel="Cancel"
    @confirm="AddMoney($event)"
    title="Deposit"
  />
  <DepositWithdrawComp
    v-else-if="WithdrawShow"
    @Cancel="Cancel"
    @confirm="WithdrawMoney($event)"
    title="Withdraw"
  />
  <SendRequest
    v-else-if="SendShow"
    @Cancel="Cancel"
    @Confirm="SendMoney($event)"
    State="Send"
  />
  <SendRequest
    v-else-if="RequestShow"
    @Cancel="Cancel"
    @Confirm="RequestMoney($event)"
    State="Request"
  />
</template>
<script>
import ButtonComp from "./ButtonComp.vue";
import DepositWithdrawComp from "./DepositWithdrawComp.vue";
import SendRequest from "./SendRequest.vue";
import TransactionComp from "./TransactionComp.vue";
import axios from "axios";
export default {
  name: "HomePage",
  components: { ButtonComp, DepositWithdrawComp, SendRequest, TransactionComp },
  data() {
    return {
      Balance:
        localStorage.getItem("Logged") == 1
          ? parseFloat(localStorage.getItem("Amount"))
          : 250,
      DepositShow: false,
      WithdrawShow: false,
      SendShow: false,
      RequestShow: false,
      username:
        localStorage.getItem("Logged") == 1
          ? localStorage.getItem("Username")
          : "Guest",
      Transactionstemp:
        localStorage.getItem("Logged") == 1
          ? localStorage.getItem("Transactions")
          : [],
      Transactions: [],
      logged: localStorage.getItem("Logged") == 1 ? 1 : 0,
      isFetching: false,
    };
  },
  created() {
    try {
      if (localStorage.getItem("Logged") == 0) {
        alert("You Need To Register To Use All The Application Features");
      }
    } catch (E) {
      localStorage.setItem("Logged", 0);
      this.Refresh();
    }
  },
  mounted() {
    localStorage.setItem("Refresh", 5);
    this.intervalId = setInterval(this.Refresh, 500);
  },

  methods: {
    Refresh() {
      if (this.logged) {
        if (localStorage.getItem("Refresh") > 0) {
          axios
            .get("http://localhost:8000/Refresh.php", {
              params: {
                email: localStorage.getItem("Email"),
              },
            })
            .then((response) => {
              if (response.data.State == 1) {
                localStorage.setItem("UserID", response.data.id);
                localStorage.setItem("Username", response.data.Username);
                localStorage.setItem("Amount", response.data.Amount);
                localStorage.setItem(
                  "Transactions",
                  JSON.stringify(response.data.Transactions)
                );
                console.log(response.data.Transactions);
              } else if (response.data.State == 0) {
                alert("Wrong Password");
              }
            })
            .catch(function (error) {
              console.error("Error:", error);
            });
          this.username =
            localStorage.getItem("Email").length > 0
              ? localStorage.getItem("Username")
              : "Guest";
          this.Transactions = [];
          const parsed = JSON.parse(localStorage.getItem("Transactions"));
          parsed.forEach((trans) => {
            this.AddTransactionToArray(
              trans.UserID,
              trans.Type,
              trans.Name,
              trans.Amount,
              trans.Date,
              trans.Color,
              trans.State
            );
          });
          this.logged = localStorage.getItem("Email").length > 0;
          const r = localStorage.getItem("Refresh") - 1;
          localStorage.setItem("Refresh", r);
        }
      }
    },
    Logout() {
      localStorage.clear();
      localStorage.setItem("Logged", 0);
      this.Transactions = [];
      this.username = "Guest";
      this.Balance = 250;
      this.logged = false;
    },
    ChangeRoute(Route) {
      this.$router.push({ name: Route });
    },
    SendToRequest(data) {
      if (this.Balance < parseFloat(data[1])) {
        alert("Insufficent Balance");
      } else {
        if (this.isFetching) return;
        this.isFetching = true;
        axios
          .post("http://localhost:8000/SendMRequest.php", {
            name: data[0],
            amount: parseFloat(data[1]),
            userID: parseInt(data[2]),
            color: "Red",
          })
          .then((response) => {
            console.log(response);
          })
          .catch(function (error) {
            console.error("Error:", error);
          })
          .finally(() => {
            this.isFetching = false;
          });
      }
    },
    getDate() {
      const currentDate = new Date();
      const day = String(currentDate.getDate()).padStart(2, "0");
      const month = String(currentDate.getMonth() + 1);
      return `${day}/${month}`;
    },
    AddTransaction(Title, Name, Amount, Color) {
      if (this.username != "Guest") {
        axios
          .post("http://localhost:8000/CreateTransaction.php", {
            userID: parseInt(localStorage.getItem("UserID")),
            type: Title,
            name: Name,
            amount: parseFloat(Amount),
            date: this.getDate(),
            color: Color,
            sendState: 0,
          })
          .then((response) => {
            if (response.data == 1) {
              this.AddTransactionToArray(
                localStorage.getItem("UserID"),
                Title,
                Name,
                Amount,
                this.getDate(),
                Color,
                false
              );
            } else {
              alert(response.data);
            }
          })
          .catch(function (error) {
            console.error("Error:", error);
          });
      } else {
        this.AddTransactionToArray(
          localStorage.getItem("UserID"),
          Title,
          Name,
          Amount,
          this.getDate(),
          Color,
          false
        );
      }
    },
    AddTransactionToArray(ID, Title, Name, Amount, Date, Color, State) {
      this.Transactions.push([ID, Title, Name, Amount, Date, Color, State]);
    },
    AddMoney(amount) {
      if (this.Balance + parseFloat(amount) <= 10000) {
        this.Balance += parseFloat(amount);
        this.DepositShow = false;
        this.AddTransaction("Deposit", "", parseFloat(amount), "Green");
      } else {
        this.DepositShow = false;
        alert("The New Balance Cant Pass 10000");
      }
    },
    WithdrawMoney(amount) {
      if (this.Balance < parseFloat(amount)) {
        alert("Insufficent Balance");
        this.WithdrawShow = false;
      } else {
        this.AddTransaction("Withdraw", "", parseFloat(amount), "Red");
        this.Balance -= parseFloat(amount);
        this.WithdrawShow = false;
      }
    },
    Deposit() {
      this.DepositShow = true;
    },
    Withdraw() {
      this.WithdrawShow = true;
    },
    SendMoney(data) {
      if (this.Balance < parseFloat(data[1])) {
        alert("Insufficent Balance");
        this.SendShow = false;
      } else {
        if (data[1] != this.username) {
          axios
            .get("http://localhost:8000/CheckUsername.php", {
              params: {
                name: data[0],
              },
            })
            .then((response) => {
              if (response.data.status === 1) {
                this.AddTransaction("Send", data[0], data[1], "Red");
                this.Balance -= parseFloat(data[1]);
              }
            })
            .catch(function (error) {
              console.error("Error:", error);
            });
          this.SendShow = false;
        } else {
          alert("You Cant Send Money To Yourself!");
        }
      }
    },
    RequestMoney(data) {
      if (this.Balance + parseFloat(data[1]) <= 10000) {
        if (data[1] != this.username) {
          axios
            .get("http://localhost:8000/CheckUsername.php", {
              params: {
                name: data[0],
              },
            })
            .then((response) => {
              if (response.data.status === 1) {
                this.AddTransaction("Request", data[0], data[1], "");
              }
            })
            .catch(function (error) {
              console.error("Error:", error);
            });
          this.RequestShow = false;
        } else {
          alert("You Cant Request Money From Yourself!");
        }
      } else {
        alert("The New Balance Cant Pass 10000");
      }
      this.RequestShow = false;
    },
    Request() {
      if (this.username != "Guest") {
        this.RequestShow = true;
      } else {
        alert("Register To Unlock This feature");
      }
    },
    Send() {
      if (this.username != "Guest") {
        this.SendShow = true;
      } else {
        alert("Register To Unlock This feature");
      }
    },
    Cancel() {
      this.DepositShow = false;
      this.WithdrawShow = false;
      this.RequestShow = false;
      this.SendShow = false;
    },
  },
};
</script>

<style>
.MainContainer {
  display: flex;
  justify-content: start;
  align-items: center;
  flex-direction: column;
  width: 100%;
  height: 80%;
}
.MainHeader {
  width: 90%;
  padding: 0 5%;
  height: 50px;
  background-color: #e8eef2;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.WelcomeText {
  font-size: 18px;
}
.LoginRegisterContainer {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 25px;
}
.headerDivider {
  width: 75%;
}
.headerContainer {
  display: flex;
  justify-content: start;
  align-items: center;
  flex-direction: column;
  width: 80%;
  padding: 0 10%;
  height: 200px;
  padding-bottom: 25px;
  background-color: #f2e8e8;
  border-bottom: 1px solid rgba(0, 0, 0, 0.455);
}
.Balance {
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: bold;
  font-size: 25px;
  width: fit-content;
  height: fit-content;
  background-color: #fcfcfc;
  border-radius: 10px;
  padding: 15px 50px;
  box-shadow: rgba(67, 71, 85, 0.27) 0px 0px 0.25em,
    rgba(90, 125, 188, 0.05) 0px 0.25em 1em;
}
.MultiButtonContainer {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 5%;
  height: 100%;
  width: 80%;
}
.TransactionsMainContainer {
  margin-top: 15px;
  position: relative;
  display: flex;
  justify-content: start;
  align-items: center;
  flex-direction: column;
  width: 100%;
  flex: 1;
  gap: 5px;
}
</style>
