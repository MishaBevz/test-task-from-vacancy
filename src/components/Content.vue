<template>
  <div class="content emergence">
    <div class="content__balance">
        <div class="content__currency"><div>Курс BTC к USD:</div> <span>{{ priceBtcForUsd }}</span></div>
        <div class="content__balance-currency"><div>Баланс USD:</div> <span>{{ content.usdBalance }}</span></div>
        <div class="content__balance-currency"><div>Баланс bBTC:</div> <span>{{ content.bbtcBalance }}</span></div>
    </div>
    <div class="content__limit-order">
        <div class="content__currency">
            <div v-bind:class="{ active: isActive }" @click="choseUsd" class="content__currency-button" id="content__currency-usd">USD</div>
            <div v-bind:class="{ active: !isActive }" @click="choseBbtc" class="content__currency-button" id="content__currency-bbtc">bBTC</div>
        </div>
        <form v-show="isActive"  v-bind:class="{ emergence: isActive }" class="content__limit-order__form">
            <div class="form-title">Купить USD за bBTC</div>
            <div class="form-content">
            <label class="form__input" >
                <span>Кол-во USD</span>
                <input type="text" name="sum-usd" @keyup="calcBtc(sumBuyUsd)" v-model.number="sumBuyUsd">
            </label>
            <label class="form__input" >
                <span>bBTC</span>
                <input type="text" name="price-btc" v-model.number="sellBbtc">
            </label>
            <div class="buttons">
                <div class="buttons__button" @click='buyUsd'>Купить</div>
            </div>
            </div>
        </form>
        
        <form v-show="!isActive" v-bind:class="{ emergence: !isActive }" class="content__limit-order__form">
            <div class="form-title">Купить bBTC за USD</div>
            <div class="form-content">
            <label class="form__input">
                <span>Кол-во bBTC</span>
                <input type="text" name="sum-btc" @keyup="calcUsd(sumBuyBbtc)" v-model.number="sumBuyBbtc">
            </label>
            <label class="form__input">
                <span>USD</span>
                <input type="text" name="price-usd" v-model.number="sellUsd">
            </label>
            <div class="buttons">
                <div class="buttons__button" @click='buyBtc'>Купить</div>
            </div>
            </div>
        </form>
    </div>
    <div class="my-orders">
        <div class="my-orders__title">My orders:</div>
        <div class="my-orders__content">
            <div v-for="(item, index) in myOrders">
                <div class="my-orders__border"></div>
                <div class="my-orders__content-items emergence"><div class="my-orders__content-items-item">{{ item }}</div><div class="my-orders__delete" @click="deleteOrder(index)">Удалить</div></div>
            </div>
        </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['content'],
  data () {
    return {
      isActive: true,
      sumBuyUsd: null,
      sellBbtc: null,
      sumBuyBbtc: null,
      sellUsd: null,
      myOrders: [],
      myOrdersArray: [],
      priceBtcForUsd: 6500
    }
  },
  methods: {
      calcBtc: function(usd){
          this.sellBbtc = Math.ceil((usd/this.priceBtcForUsd)*100000000)/100000000
      },
      calcUsd: function(bbtc){
          this.sumBuyBbtc = bbtc.toFixed(8);
          this.sellUsd = Math.ceil((bbtc*this.priceBtcForUsd)*100)/100;
      },
      deleteOrder: function(index) {
          if(this.myOrdersArray[index].buy === 'usd') {
              this.content.usdBalance = this.content.usdBalance - this.myOrdersArray[index].usd
              this.content.bbtcBalance = this.content.bbtcBalance + this.myOrdersArray[index].bbtc
          } 
          else if(this.myOrdersArray[index].buy === 'bbtc') {
              this.content.bbtcBalance = this.content.bbtcBalance - this.myOrdersArray[index].bbtc
              this.content.usdBalance = this.content.usdBalance + this.myOrdersArray[index].usd
          } 
          this.myOrders.splice(index, 1);
          this.myOrdersArray.splice(index, 1)
      },
      choseUsd: function() {
          this.isActive = true
      },
      choseBbtc: function() {
          this.isActive = false
      },

      buyUsd: function() {
          if(!this.sumBuyUsd || !this.sellBbtc) {
              alert('Ошибка валидации, заполните все поля')
          } 
          else if(!Number(this.sumBuyUsd) || !Number(this.sellBbtc)) {
              alert('Ошибка валидации, введите числовое значение')
          }
          else if(Number(this.sumBuyUsd) && Number(this.sellBbtc)) {  
            if(Number(this.sellBbtc) > this.content.bbtcBalance){
                alert('Вы ввели сумму, превышающую текущий баланс bBTC')
            }
            else if(this.lastBuyBbtc > 0 && this.sellBbtc > this.lastBuyBbtc + (this.lastBuyBbtc * 0.1)){
                alert('Сумма bBTC не должна отличаться на 10% от предыдущей покупки')
            }
            else {
                alert('Успех')
                this.myOrders.push(`Покупка ${this.sumBuyUsd} USD за ${this.sellBbtc} bBTC`);
                this.myOrdersArray.push({ buy: 'usd', usd: this.sumBuyUsd, bbtc: this.sellBbtc});    
                this.content.bbtcBalance = Number(this.content.bbtcBalance) - Number(this.sellBbtc);
                this.content.usdBalance = Number(this.content.usdBalance) + Number(this.sumBuyUsd);
                this.sumBuyUsd = null;
                this.sellBbtc = null;
            }
          } 
      },
      buyBtc: function() {
          if(!this.sumBuyBbtc || !this.sellUsd) {
              alert('Ошибка валидации, заполните все поля')
          } 
          else if(!Number(this.sumBuyBbtc) || !Number(this.sellUsd)) {
              alert('Ошибка валидации, введите числовое значение')
          }
          else if(Number(this.sumBuyBbtc) && Number(this.sellUsd)) {  
            if(Number(this.sellUsd) > this.content.usdBalance){
                alert('Вы ввели сумму, превышающую текущий баланс USD')
            }
            else if(this.lastBuyUsd > 0 && this.sellUsd > this.lastBuyUsd + (this.lastBuyUsd * 0.1)){
                alert('Сумма USD не должна отличаться на 10% от предыдущей покупки')
            }
            else {
                alert('Успех')
                this.myOrders.push(`Покупка ${this.sumBuyBbtc} bBTC за ${this.sellUsd} USD`);
                this.myOrdersArray.push({ buy: 'bbtc', usd: this.sellUsd, bbtc: this.sumBuyBbtc});
                this.content.usdBalance = Number(this.content.usdBalance) - Number(this.sellUsd);
                this.content.bbtcBalance = Number(this.content.bbtcBalance) + Number(this.sumBuyBbtc);
                this.sumBuyBbtc = null;
                this.sellUsd = null;
            }
          } 
      },
  },
  watch: {
      
  }
}
</script>

<style lang="scss" scoped>
.active {
    opacity: 1 !important;
}
.emergence {
    animation: emergence 1s normal;
    opacity: 1;
    @keyframes emergence {
        0% {opacity: 0}
        100% {opacity: 1}
    }
}

.content {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    .my-orders {  
        margin-top: 30px;
        display: flex;
        flex-direction: column;
        justify-content: center;
        align-items: center;
        .my-orders__content {
            display: flex;
            flex-direction: column-reverse;
            justify-content: center;
            align-items: center;
            .my-orders__title {
                font-size: 18px;
            }
            .my-orders__border {
                border: 1px solid royalblue;
                width: 388px;
                margin-top: 10px;
                margin-bottom: 10px;
            }
            .my-orders__content-items {
                display: flex;
                justify-content: space-between;
            }
            .my-orders__delete {
                color: red;
                cursor: pointer;
            }
            .my-orders__content-items-item {
                    overflow-x: auto;
                    width: 320px;
                    white-space: nowrap;
            }
        }
        
    }
    .content__balance {
        min-width: 303px;
        .content__currency {
            display: flex;
            justify-content: center;
        }
        .content__balance-currency, .content__currency {
            display: flex;
            justify-content: flex-start;
            font-size: 18px;
            div {
                width:150px
            }
            span {
                color: green;
            }
        }
    }  
    .content__limit-order {
        display: flex;
        width: 646px;
        justify-content: center;
        margin-top: 30px;
        .content__currency {
            margin-top: 10px;
            .content__currency-button {
                background-color: royalblue;
                cursor: pointer;
                transition: opacity .20s ease-in-out;
                color: white;
                width: 40px;
                height: 18px;
                font-size: 14px;
                display: flex;
                justify-content: center;
                align-items: center;
                opacity: 0.8;   
                &:nth-child(2) {
                    margin-top: 2px;
                }
                &:hover {
                    opacity: 1
                }
                
            }
        }
        form {
            display: flex;
            flex-direction: column;
            width: 350px;
            .form-title {
                display: flex;
                align-items: center;
                padding-left: 20px;
                background-color: #F8F8FF;
                height: 35px;
            }  
            .form-content {
                background-color: whitesmoke;
                label {
                    margin-top: 10px;
                }
                .form__input {
                    display: flex;
                    justify-content: space-between;
                    padding-right: 20px;
                    padding-left: 20px;
                    span {
                        width: 100%;
                    }
                    input {
                        width: 180px;
                        height: 20px;
                        border: none;
                    }
                }
                .buttons {
                    display: flex;
                    justify-content: flex-end;
                    margin-top: 10px;
                    margin-bottom: 10px;
                    .buttons__button {
                        display: flex;
                        justify-content: center;
                        align-items: center;
                        background-color: royalblue;
                        color: white;
                        border: none;
                        width: 168px;
                        height: 40px;
                        margin-right: 20px;
                        cursor: pointer;
                        transition: opacity .20s ease-in-out;
                        &:hover {
                            opacity: 0.8
                        }
                    }
                }
            }
            
        }
        
    }    
}
    
</style>
