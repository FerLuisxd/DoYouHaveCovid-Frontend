<template>
  <div class="container">
    <div>
      <div>
        <el-row>
          <el-col :span="7">
            <el-input placeholder="Start" v-model="start"></el-input>
          </el-col>
          <el-col :span="7">
            <el-input placeholder="End" v-model="end"></el-input>
          </el-col>
          <el-col :span="5">
            <el-button @click="search">Buscar</el-button>
          </el-col>
          <el-col :span="5">
            <el-button @click="clearFilter">Limpiar Filtro</el-button>
          </el-col>
        </el-row>
      </div>
      <el-table ref="filterTable" :data="tableData" style="width: 100%">
        <el-table-column prop="hash" label="Hash" width="180"></el-table-column>
        <el-table-column prop="precedingHash" label="PrecedingHash" width="180"></el-table-column>
        <el-table-column prop="symptoms" label="Sintomas" width="250"></el-table-column>
        <el-table-column
          prop="severity"
          label="Etiqueta"
          width="100"
          :filters="[{ text: 'Grave', value: 'Grave' }, { text: 'Moderado', value: 'Moderado' }, { text: 'Leve', value: 'Leve' }, { text: 'Sin Gravedad', value: 'Sin gravedad' }]"
          :filter-method="filterTag"
          filter-placement="bottom-end"
        >
          <template slot-scope="scope">
            <el-tag type="success" disable-transitions>{{scope.row.severity}}</el-tag>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
import swal from "sweetalert2/dist/sweetalert2.all.min.js";
export default {
  data() {
    return {
      tableData: [
        {
          date: "2016-05-03",
          name: "Tom",
          address: "No. 189, Grove St, Los Angeles",
          tag: "Home"
        },
        {
          date: "2016-05-02",
          name: "Tom",
          address: "No. 189, Grove St, Los Angeles",
          tag: "Office"
        },
        {
          date: "2016-05-04",
          name: "Tom",
          address: "No. 189, Grove St, Los Angeles",
          tag: "Home"
        },
        {
          date: "2016-05-01",
          name: "Tom",
          address: "No. 189, Grove St, Los Angeles",
          tag: "Office"
        }
      ],
      start: 0,
      end: 10
    };
  },
  methods: {
    search() {
      let urlRequest =
      "http://localhost:8000/limited/" + this.start + "/" + this.end;
    let request = new Request(urlRequest, {
      method: "GET",
      mode: "cors"
    });
    let response = fetch(request)
      .then(res => res.json())
      .then(prediction => {
        let array = [];
        for (let i = 0; i < prediction.length; i++) {
        let nivel = ""
          switch (prediction[i].data.severity) {
            case 3:
              nivel = "Leve"
              break;
            case 6:
              nivel = "Moderado"
              break;
            case 10:
              nivel = "Grave"
              break;
            case 0:
              nivel = "Sin gravedad"
              break;
          }
          let symptoms = ""
          if(prediction[i].data.diarrhea) symptoms+= "Diarrea,"
          if(prediction[i].data.runny_Nose) symptoms+= "Nariz tupida,"
          if(prediction[i].data.difficulty_in_Breathing) symptoms+= "Dificultad respitatoria,"
          if(prediction[i].data.dry_Cough) symptoms+= "Tos seca,"
          if(prediction[i].data.fever) symptoms+= "Fiebre,"
          if(prediction[i].data.nasal_Congestion) symptoms+= "Congestion Nasal,"
          if(prediction[i].data.pains) symptoms+= "Dolor,"
          if(prediction[i].data.sore_Throat) symptoms+= "Dolor de garganta,"
          if(prediction[i].data.tiredness) symptoms+= "Cansacio,"
          prediction[i].data.severity = nivel
          prediction[i].data.symptoms = symptoms
          prediction[i].data.hash = prediction[i].hash;
          prediction[i].data.precedingHash = prediction[i].precedingHash;
          array.push(prediction[i].data);
        }
        this.tableData = array;
        console.log(this.tableData);

      });
    },
    resetDateFilter() {
      this.$refs.filterTable.clearFilter("date");
    },
    clearFilter() {
      this.$refs.filterTable.clearFilter();
    },
    formatter(row, column) {
      return row.address;
    },
    filterTag(value, row) {
      return row.severity === value;
    },
    filterHandler(value, row, column) {
      const property = column["property"];
      return row[property] === value;
    }
  },
  mounted: function() {
    this.search()
  }
};
</script>

<style>
.container {
  margin: 0 auto;
  padding-top: 2%;
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
