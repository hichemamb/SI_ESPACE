<template>
  <div class="register-page">
    <helperBackground :imgSrc="img" />
    <register-form @stepData="processStepData" :default-value="actualStep.defaultValue" :input-type="actualStep.ftype" :input-placeholder="actualStep.placeholder" :label-info="actualStep.title" :field-name="actualStep.fname"/>
    <register-steps :steps="stepsData"/>
  </div>
</template>

<script>
import axios from "axios";

import router from "../router/index.js";
import RegisterForm from "@Component/Register/Form.vue";
import RegisterSteps from "@Component/Register/Steps.vue";
import HelperBackground from "@Component/Helper/Background.vue";

import myBg from "../img/boutique.jpg";
import "@ViewStyle/Register.scss";
import api from "../APIHelper.js";

export default {
  name: "Register",
  components: {
    HelperBackground,
    RegisterForm,
    RegisterSteps
  },
  data() {
    return {
      img: myBg,
      actualStep: {},
      stepsData: {
        username: {
          placeholder: "Votre nom d'utilisateur",
          title: "Choisissez un nom d'utilisateur :",
          fname: "username",
          ftype: "text",
          defaultValue: "",
          nextStep: "email",
          isDone: true
        },
        email: {
          placeholder: "Entrez votre e-mail",
          title: "Veuillez entrer une adresse e-mail :",
          fname: "email",
          ftype: "email",
          defaultValue: "",
          nextStep: "plainPassword",
          isDone: false
        },
        plainPassword: {
          placeholder: "Votre mot de passe",
          title: "Veuillez choisir un mot de passe :",
          fname: "plainPassword",
          ftype: "password",
          defaultValue: "",
          nextStep: "lastName",
          isDone: false
        },
        lastName: {
          placeholder: "Entrez votre nom",
          title: "Veuillez renseigner votre nom :",
          fname: "lastName",
          ftype: "text",
          defaultValue: "",
          nextStep: "firstName",
          isDone: false
        },
        firstName: {
          placeholder: "Entrez votre prénom",
          title: "Veuillez renseigner votre prénom :",
          fname: "firstName",
          ftype: "text",
          defaultValue: "",
          nextStep: "phoneNumber",
          isDone: false
        },
        phoneNumber: {
          placeholder: "Entrez votre numéro",
          title: "Veuillez renseigner un numéro de téléphone :",
          fname: "phoneNumber",
          ftype: "text",
          defaultValue: "",
          nextStep: "planet",
          isDone: false
        },
        planet: {
          placeholder: "Entrez le nom de votre planète",
          title: "De quelle planète êtes-vous originaire ?",
          fname: "planet",
          ftype: "text",
          defaultValue: "",
          nextStep: "credits",
          isDone: false
        },
        credits: {
          placeholder: "Entrez le montant de vos crédits",
          title: "De combien souhaitez-vous créditer votre compte ?",
          fname: "credits",
          ftype: "number",
          defaultValue: "0",
          nextStep: "",
          isDone: false
        }
      }
    };
  },
  created() {
    this.stepsData.username.defaultValue =
      this.$store.getters.registrationDetails.username ||
      this.stepsData.username.defaultValue;
    this.actualStep = this.stepsData.username;
    this.$store.commit("setValidationErrors", []);
  },
  methods: {
    handleRegistrationSubmit() {
      console.log(this.$store.getters.registrationDetails);
      api
        .post("/user", this.$store.getters.registrationDetails)
        .then(response => {
          router.push("/login");
        })
        .catch(error => {
          this.$store.commit(
            "setValidationErrors",
            Object.values(
              error.response.data["additional-informations"][
                "fields-validation-violations"
              ]
            )
          );
          this.redirectToConfirmation();
        });
    },
    redirectToConfirmation() {
      router.push("/confirmation");
    },
    processStepData(object) {
      this.$store.commit("addRegistrationDetails", [object.type, object.input]);
      console.log(this.$store.getters.registrationDetails);
      if ("" === this.actualStep.nextStep) {
        this.handleRegistrationSubmit();
      } else {
        this.stepsData[this.actualStep.nextStep].defaultValue =
          this.$store.getters.registrationDetails[this.actualStep.nextStep] ||
          this.stepsData[this.actualStep.nextStep].defaultValue;
        this.actualStep = this.stepsData[this.actualStep.nextStep];
        this.actualStep.isDone = true;
      }
    }
  }
};
</script>
