<template>
  <div class="about">
    <h1>This is an about page</h1>
    <button id="your-button">paypal</button>
    <div v-if="!paidFor">
      <div v-for="item in products" :key="item.id">
        <h2>{{ item.name }}</h2>
        <img :src="`${item.thumbnail}`" width="350" />
        <span>{{ item.price }}</span>
      </div>
      <div ref="paypal"></div>
    </div>
  </div>
</template>
<script>
import { loadScript } from "@paypal/paypal-js";
export default {
  data: () => {
    return {
      result: null,
      loaded: false,
      paidFor: false,
      products: [
        {
          id: "01",
          name: "Estufa",
          price: "$ 15,400.00",
          thumbnail: "https://picsum.photos/400",
        },
      ],
      description: "",
      total: 0,
    };
  },
  mounted() {
    this.callApi();
    this.setLoad();
  },
  methods: {
    callApi() {
      let paypal;
      try {
        paypal = loadScript({
          "client-id":
            "Ac4JC1Y7F5o648nRm_8xxvuETCWTjnPKyZ9aUcHXFHgTMTQUo0N89FFA1Ino63NeMOMd36ez6M_m6al0",
        });
      } catch (error) {
        console.log("failed to load the Paypal JS SDK", error);
      }
      if (paypal) {
        try {
          paypal.Buttons().render("your-button");
        } catch (error) {
          console.log("failed to render the paypal Buttons", error);
        }
      }
    },
    setLoad: function () {
      this.loaded = true;
      window.paypal
        .Buttons({
          createOrder: (data, actions) => {
            return actions.order.create({
              purchase_units: [
                {
                  description: this.products.name,
                  amount: {
                    currency_code: "USD",
                    value: this.products.price,
                  },
                },
              ],
            });
          },
          onApprove: async (data, actions) => {
            const order = await actions.order.capture();
            this.data;
            this.paidFor = true;
             console.log(order);
          },
          onError: (err) => {
            console.log(err);
          },
        })
        .render(this.$refs.paypal);
    },
  },
};
</script>
