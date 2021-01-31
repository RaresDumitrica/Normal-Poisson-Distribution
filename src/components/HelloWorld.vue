<template>
  <el-row class="hello">
    <el-col :span ="8" :offset="8">
      <h1>{{ msg }}</h1>
      <h2>Media Teoretica: {{theoreticMean }} | Dispersia Teoretica: {{this.theoreticDispersion}}</h2>
        <el-button @click="clt" type="success" round plain>CLT</el-button>
      <el-row>
        <el-button @click="runThousandTimes" type="success" round plain >Generate Many Values</el-button>
        <el-button @click="verifyMean" type="success" round plain >Calculate Results Mean</el-button>
      </el-row>
      <el-card class="resultDiv">
        Ultima variabila generata: {{ result }}
      </el-card>
      <el-card class="resultDiv">
        Media Empirica: {{ averageResult }}
      </el-card>
      <el-card class="resultDiv">
        Dispersia Empirica: {{ cltDispersion }}
      </el-card>
    </el-col>
    <el-col :span ="8" :offset="8">
      <el-card class="clearfix resultDiv">
        <span>Istoric rezultate</span>
      </el-card> 
    <el-card class="box-card" style="overflow:auto">
        <div v-for="element in resultsArray.length" :key="element" class="text item infinite-list-item" >
        {{'Variabila ' + element +  ') ' + resultsArray[element-1]  }}
      </div>
    </el-card>
    </el-col>
  </el-row>
</template>

<script>
import Swal from 'sweetalert2'
export default {
  name: 'HelloWorld',
  data: function () {
    return {
      msg: "Central Limit Theorem| N(0.2, 3)",
      result: null,
      resultsArray: [],
      averageResult: null,
      cltDispersion: null,
      theoreticMean: 0.2,
      theoreticDispersion: Math.pow(3, 2),
    }
  },
  methods: {
    generateNumberRandom(upper, lower) {
      // Se genereaza numar random intre lower si upper
      return Math.random() * (upper-lower) + lower;
    },
    
    N01()  {
        // Se calculeaza N(0,1)
        let suma = 0.0;
        let X = 0;

        for (var i = 0; i < 12; i += 1) {
            let r = Math.random()
            suma += r;
        }
        X = ( suma - 6);

        return X;
    },
    LimitaCentrala(miu, sigma) {
        // Se calculeaza N(0, 1)
        let Z = this.N01();
        // Se calculeaza N(miu, sigma)
        let X = miu + ( sigma * Z  );
       
        return X;
    },
    clt() {
      let Zn = this.LimitaCentrala(0.2, 3)
      this.result = Zn;
      this.resultsArray.push(this.result);
      console.log(this.resultsArray);
      
    },
    
    verifyMean() {
      let myArray = this.resultsArray;
      if(myArray.length > 0) {
          var total = 0;
        for(let element = 0; element < myArray.length; element++) {
          total += myArray[element]; 
        }
        console.log("total", total);

        let averageMean = (total / myArray.length);
        console.log("Media= ", averageMean);
        this.averageResult = averageMean;

        
        this.calculateDispersion(myArray, averageMean);
      } else {
        Swal.fire({
          icon: 'error',
          title: 'Oops...',
          text: 'Nu ai destule elemente pentru a calcula media',
        });
      }
      
    },

    calculateDispersion(array, mean) {
       var sum = 0;
      for(let element = 0; element < array.length; element++) {
          let Xi2 = Math.pow((array[element]), 2);
          sum += Xi2;
          // console.log("X"+ element, array[element])
      }
      if(array.length >= 2) {
        console.log("sum= ",sum);
        let X2 = Math.pow(mean, 2);
        let dispersion = ((sum/array.length) - X2);
        // console.log("Dispersia=", dispersion);
        this.cltDispersion = dispersion;
      } else {
        Swal.fire({
          icon: 'error',
          title: 'Oops...',
          html: 'Nu ai destule elemente pentru a calcula dispersia <br> Media a fost calculata cu 1 element'
        });
      }
    } ,
    runThousandTimes() {
      for(let element= 0; element < ((Math.random() * 10000) + 3000); element++) {
        console.log(element + 1);
        this.clt();
      }
    },
  }
}
</script>

<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.resultDiv{
  margin-top: 30px;
}

button {
  margin-top: 20px;
}

.text {
    font-size: 14px;
  }

  .item {
    margin-bottom: 12px;
  }

  .clearfix:before,
  .clearfix:after {
    display: table;
    content: "";
  }
  .clearfix:after {
    clear: both
  }

  .box-card {
    height: 250px;
  }
</style>
