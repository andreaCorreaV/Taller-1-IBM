<template>
  <div class="posts">
    <h1>Elecciones Presidenciales 2019</h1>
    <h4>Si es un votante registrado, ingrese su ID de votante a continuación</h4>
    <form v-on:submit="validateVoter">
      <input type="text" v-model="loginData.voterId" placeholder="Ingresar ID">
      <br>

      <input type="submit" value="Ingresar">
      <br>
      <br>
      <span v-if="loginReponse">
        <b>{{ loginReponse.data }}</b>
      </span>
      <br>
    </form>

    <br>
    <h4>De lo contrario, complete el siguiente formulario para registrarse.</h4>
    <form v-on:submit="registerVoter">
      <input type="text" v-model="registerData.voterId" placeholder="Ingrese su Cedula de Identidad">
      <br>
      <input type="text" v-model="registerData.registrarId" placeholder="Credencial Civica">
      <br>
      <input type="text" v-model="registerData.firstName" placeholder="Nombre">
      <br>
      <input type="text" v-model="registerData.lastName" placeholder="Apellido">
      <br>
      <input type="submit" value="Registrarse">
    </form>
    <br>
    <span v-if="registerReponse">
      <b>{{ registerReponse.data }}</b>
    </span>
    <br>
    <vue-instant-loading-spinner id='loader' ref="Spinner"></vue-instant-loading-spinner>
  </div>
</template>

<script>
import PostsService from "@/services/apiService";
import VueInstantLoadingSpinner from "vue-instant-loading-spinner/src/components/VueInstantLoadingSpinner.vue";

export default {
  name: "response",
  data() {
    return {
      loginData: {},
      registerData: {},
      registerReponse: {
        data: ""
      },
      loginReponse: {
        data: ""
      }
    };
  },
  components: {
    VueInstantLoadingSpinner
  },
  methods: {
    async registerVoter() {

      await this.runSpinner();
      const apiResponse = await PostsService.registerVoter(
        this.registerData.voterId,
        this.registerData.registrarId,
        this.registerData.firstName,
        this.registerData.lastName
      );

      console.log(apiResponse);
      this.registerReponse = apiResponse;
      await this.hideSpinner();
    },

    async validateVoter() {
      await this.runSpinner();

      if (!this.loginData.voterId) {
        console.log("!thislogin");
        let response = 'Ingrese su ID de votante';
        this.loginReponse.data = response;
        await this.hideSpinner();
      } else {
        const apiResponse = await PostsService.validateVoter(
          this.loginData.voterId
        );
        console.log("apiResponse");
        console.log(apiResponse.data);

        if (apiResponse.data.error) {
          console.log(apiResponse.data.error);
          this.loginReponse = apiResponse.data.error;
        } else {
          this.$router.push("castBallot");
        }

        console.log(apiResponse);
        this.loginReponse = apiResponse;
        await this.hideSpinner();
      }
    },
    async runSpinner() {
      this.$refs.Spinner.show();
    },
    async hideSpinner() {
      this.$refs.Spinner.hide();
    }
  }
};
</script>
