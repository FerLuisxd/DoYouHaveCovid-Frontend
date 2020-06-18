<template>
  <div class="container">
    <div>
      <el-form ref="form" :model="form" label-width="50%">
        <el-form-item label="Fiebre">
          <el-switch v-model="form.fever"></el-switch>
        </el-form-item>
        <el-form-item label="Cansancio">
          <el-switch v-model="form.tiredness"></el-switch>
        </el-form-item>
        <el-form-item label="Tos seca">
          <el-switch v-model="form.dry_Cough"></el-switch>
        </el-form-item>
        <el-form-item label="Dificultad al respirar">
          <el-switch v-model="form.difficulty_in_Breathing"></el-switch>
        </el-form-item>
        <el-form-item label="Dolor de garganta">
          <el-switch v-model="form.sore_Throat"></el-switch>
        </el-form-item>
        <el-form-item label="Dolor general">
          <el-switch v-model="form.pains"></el-switch>
        </el-form-item>
        <el-form-item label="Moqueo en la nariz">
          <el-switch v-model="form.nasal_Congestion"></el-switch>
        </el-form-item>
        <el-form-item label="Moqueo de nariz">
          <el-switch v-model="form.runny_Nose"></el-switch>
        </el-form-item>
        <el-form-item label="Diarrea">
          <el-switch v-model="form.diarrhea"></el-switch>
        </el-form-item>
        <el-form-item label="Contacto con alguien con Covid?">
          <el-select v-model="form.contact" placeholder="Selecciona una opcion">
            <el-option label="Si" value="1"></el-option>
            <el-option label="No estoy seguro" value="0.5"></el-option>
            <el-option label="No" value="0"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="Genero">
          <el-select v-model="form.gender" placeholder="Selecciona tu genero">
            <el-option label="Masculino" value="1"></el-option>
            <el-option label="Femenino" value="0.5"></el-option>
            <el-option label="Indefinido" value="0"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="Edad">
          <el-radio-group v-model="form.age">
            <el-radio label="0">Entre 0 y 9</el-radio>
            <el-radio label="0.25">Entre 10 y 19</el-radio>
            <el-radio label="0.5">Entre 20 y 24</el-radio>
            <el-radio label="0.75">Entre 25 y 29</el-radio>
            <el-radio label="1">Mas de 60</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="onSubmit">Enviar</el-button>
          <el-button @click="reset">Cancelar</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import swal from "sweetalert2/dist/sweetalert2.all.min.js";
export default {
  data() {
    return {
      form: {
        fever: false,
        tiredness: false,
        dry_Cough: false,
        difficulty_in_Breathing: false,
        none_Sympton: false,
        sore_Throat: false,
        pains: false,
        nasal_Congestion: false,
        diarrhea: false,
        none_Experiencing: false,
        runny_Nose: false,
        age: null,
        gender: 0,
        severity: 0,
        contact: null
      }
    };
  },
  methods: {
    onSubmit() {
      let requestBody = JSON.parse(JSON.stringify(this.form))
      if (
        !requestBody.fever &&
        !requestBody.tiredness &&
        !requestBody.difficulty_in_Breathing
      )
        requestBody.none_Sympton = true;
      if (
        !requestBody.sore_Throat &&
        !requestBody.pains &&
        !requestBody.nasal_Congestion &&
        !requestBody.diarrhea
      )
        requestBody.none_Experiencing = true;

      requestBody.fever= new Number(requestBody.fever)
      requestBody.tiredness= new Number(requestBody.tiredness)
      requestBody.dry_Cough= new Number(requestBody.dry_Cough)
      requestBody.difficulty_in_Breathing= new Number(requestBody.difficulty_in_Breathing)
      requestBody.none_Sympton= new Number(requestBody.none_Sympton)
      requestBody.sore_Throat= new Number(requestBody.sore_Throat)
      requestBody.pains= new Number(requestBody.pains)
      requestBody.nasal_Congestion= new Number(requestBody.nasal_Congestion)
      requestBody.diarrhea= new Number(requestBody.diarrhea)
      requestBody.none_Experiencing= new Number(requestBody.none_Experiencing)
      requestBody.runny_Nose= new Number(requestBody.runny_Nose)


      requestBody.age = new Number(requestBody.age);
      requestBody.gender = new Number(requestBody.gender);
      requestBody.contact = new Number(requestBody.contact);
      console.log("age", requestBody.age);
      console.log("submit!", requestBody);
      let request = new Request("http://localhost:4000/api/neuronal/v2", {
        method: "POST",
        mode: "cors",
        body: JSON.stringify(requestBody)
      });
      fetch(request)
        .then(res => res.json())
        .then(prediction => {
          // this.$swal(JSON.stringify(prediction));
          this.$swal({
            title: "Prediccion",
            text: "Nivel de gravedad de covid " + prediction.knn,
            type: "success"
          });
        });
    },
    reset() {
      console.log("submit!");
    }
  }
};
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: "Quicksand", "Source Sans Pro", -apple-system, BlinkMacSystemFont,
    "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
