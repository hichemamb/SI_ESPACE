<template>
  <div class="booking-page">
    <helperBackground :imgSrc="img" />
    <booking-form @stepData="processStepData" :default-value="actualStep.defaultValue" :selected="actualStep.selected" :input-type="actualStep.ftype" :input-placeholder="actualStep.placeholder" :label-info="actualStep.title" :field-name="actualStep.fname" :select="actualStep.select" :options="actualStep.options" />
    <p  class="booking-form__link" v-on:click="redirectToBookingAdd" v-if="actualStep.add">Ajouter un vaisseau</p>
  </div>
</template>

<script>
import axios from "axios";

import router from "../router/index.js";
import BookingForm from "@Component/Booking/Form.vue";
import HelperBackground from "@Component/Helper/Background.vue";

import imageBg from "../img/alpha.png";

import api from "../APIHelper.js";

export default {
  name: "Booking",
  components: {
    HelperBackground,
    BookingForm
  },
  data() {
    return {
      actualStep: {},
      img: imageBg,
      spaceships: [],
      stepsData: {
        name: {
          select: true,
          add: true,
          options: [],
          title: "Pour quel vaisseau souhaitez-vous réserver une place ? ",
          fname: "name",
          ftype: "hidden",
          defaultValue: "",
          placeholder: "",
          nextStep: "date",
          selected: ""
        },
        date: {
          select: false,
          add: false,
          title: "Quand est-ce que vous nous rejoingnez ?",
          fname: "date",
          ftype: "date",
          defaultValue: "",
          nextStep: "people",
          placeholder: "Entrez votre date",
          options: []
        },
        people: {
          select: false,
          add: false,
          title: "Combien de personnes êtes-vous ?",
          fname: "people",
          ftype: "number",
          defaultValue: "0",
          nextStep: "confirm",
          placeholder: "Nombre de personnes",
          options: []
        },
        confirm: {
          select: false,
          add: false,
          title: "Votre réservation a bien été prise en compte",
          fname: "comfirm",
          ftype: "hidden",
          defaultValue: "",
          nextStep: "",
          placeholder: "",
          options: []
        }
      }
    };
  },
  beforeMount() {
    if (true !== this.$store.getters.isLoggedIn) {
      router.push("/login");
      return;
    }
    api
      .get("/spaceship")
      .then(response => {
        let spaceShipDatas = [];
        response.data.results.spaceships.forEach(element => {
          spaceShipDatas.push({
            text: element.matricule,
            value: element.id
          });
          this.spaceships.push(element);
        });
        this.stepsData.name.options = spaceShipDatas;
        this.stepsData.name.selected = this.spaceships[0].id.toString();
        this.actualStep = this.stepsData.name;
      })
      .catch(error => {
        // @TODO: handle errors instead of doing crap
        console.error(error.response.data);
        console.log(this.spaceshipDetails);
      });
  },
  methods: {
    redirectToBookingAdd() {
      router.push("/bookingadd");
    },
    processStepData(object) {
      if ("" === this.actualStep.nextStep) {
        router.push("/bookingconfirmation");
      } else {
        this.actualStep = this.stepsData[this.actualStep.nextStep];
      }
    }
  }
};
</script>
