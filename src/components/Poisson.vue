<template>
  <el-row class="hello">
    <el-col>
        <el-row>
        <h1>{{ msg }} {{this.lamda}}</h1>
        <el-row>
            <el-input-number v-model="lamda" controls ></el-input-number>
        </el-row>
        <el-row>
        <el-button @click="Poisson" type="success">Generate Poisson Variable</el-button>
        </el-row>
        <el-button @click="runThousandTimes" type="info">Generate Many Variables </el-button>
        <el-button @click="verifyMean" type="info" >Empiric Mean and Dispersion</el-button>
        </el-row>
        <el-col :span="8" :offset="3">
            <el-card class="resultDiv">
                 Ultima variabila Poisson:  {{ result }}
            </el-card>
            <el-card class="resultDiv">
                Media Empirica: {{ averageResult }}
            </el-card>
            <el-card class="resultDiv">
              Dispersia Empirica: {{ poissonDispersion }}
            </el-card>        
            <el-card class="clearfix resultDiv">
                <span>Istoric rezultate</span>
            </el-card> 
            <el-card class="box-card" style="overflow:auto">
                <div v-for="element in resultsArray.length" :key="element" class="text item infinite-list-item" >
                {{'Variabila Poisson ' + element +  ') ' + resultsArray[element-1]  }}
            </div>
            </el-card>
        </el-col>
        <el-col :span="8" :offset="2">
           <el-card class="resultDiv" style="background-color: #F8F9FA">
                Ultima variabila Poisson Bionmiala:  {{ result2 }}
            </el-card>
           <el-card class="resultDiv">
                Media Empirica Binom: {{ averageResultBinom }}
            </el-card>
            <el-card class="resultDiv">
              Dispersia Empirica Binom: {{ poissonDispersionBinom }}
            </el-card>
            
            <el-card class="clearfix resultDiv">
                <span>Istoric rezultate cu Binom</span>
            </el-card> 
            <el-card class="box-card" style="overflow:auto; scroll-behavior: smooth">
                <div v-for="element in resultsArrayBinom.length" :key="element" class="text item infinite-list-item" >
                {{'Variabila Poisson ' + element +  ') ' + resultsArrayBinom[element-1]  }}
            </div>
            </el-card>
        </el-col>
    </el-col>
  </el-row>
</template>

<script>
import Swal from 'sweetalert2'

export default {
  name: 'Poisson',
  data: function () {
    return {
      msg: "Poisson Variable Generation in 2 ways || Media si Dispersia teoretica = ",
      result: null,
      result2: null,
      lamda: 1,
      resultsArray: [],
      averageResult: null,
      resultsArrayBinom: [],
      averageResultBinom: null,
      poissonDispersion: null,
      poissonDispersionBinom: null,
    }
  },
  methods: {
    generateNumberRandom(upper, lower) {
      return Math.random() * (upper-lower) + lower;
    },

    Poisson() {
        console.log(this.lamda);
        if(this.lamda !== null && this.lamda !== undefined && this.lamda > 0) {            
            var landa = this.lamda;
            console.log("landa", landa);
            let i = 0;
            let P = 1;
            let exponentialPower = Math.exp(-landa);

            do {
                let U = this.generateNumberRandom(0, 1);
                console.log(U);
                i+=1;
                P *= U
            }
            while(P >= exponentialPower)
            console.log("i", i)
            let X = i-1;
            console.log("Variabila Poisson", X);
            this.result = X;
            this.resultsArray.push(X);

            this.PoissonWithBinomal();
        } else {
            console.log("Nu merge daca e 0");
        }
    },

    PoissonWithBinomal() {
        const landa = this.lamda;
        const p = 0.001;
        const n = (landa/p).toFixed();
        const x= this.getBinomial(n,p);
        console.log("Cu binom", x);
        this.result2 = x;
        this.resultsArrayBinom.push(x);

    },
    getBinomial(n, p) {
        let x = 0;
        for(let element = 0; element < n; element++) {
            if(Math.random() < p)
            x++;
        }
        return x;
    },

    verifyMean() {
        let myArray = this.resultsArray;
        let myArrayBinom = this.resultsArrayBinom;
        console.log("Array", myArray);
        console.log("Array cu Binom", myArrayBinom);
        if(myArray.length > 0) {
          var total = 0;
          var totalBinom = 0;
          for(let element = 0; element < myArray.length; element++) {
              total += myArray[element]; 
          }
          for(let element = 0; element < myArrayBinom.length; element++) {
              totalBinom += myArrayBinom[element]; 
          }

          console.log("total", total);
          console.log("total cu Binom", totalBinom);

          let averageMean = (total / myArray.length);
          this.averageResult = averageMean;
          console.log("media", averageMean);

          let averageMeanBinom = (totalBinom/ myArrayBinom.length);
          this.averageResultBinom = averageMeanBinom;
          console.log("media binom", averageMeanBinom);

          let dispersionBinom = 0;
          this.poissonDispersion = this.calculateDispersion(myArray, averageMean, this.poissonDispersion);
          dispersionBinom = this.calculateDispersion(myArrayBinom, averageMeanBinom, dispersionBinom);
          this.poissonDispersionBinom =  dispersionBinom;

        } else {
          Swal.fire({
            icon: 'error',
            title: 'Oops...',
            text: 'Nu ai destule elemente pentru a calcula media',
          });
        }
         
    },

    calculateDispersion(array, mean, dispersion) {
       var sum = 0;
      for(let element = 0; element < array.length; element++) {
          let Xi2 =  Math.pow(array[element], 2);
          sum += Xi2;
          console.log("X"+ element, array[element])
      }
      if(array.length >= 2) {
        let X2 = Math.pow(mean, 2) 
        console.log("sum= ",sum);
        dispersion = ((sum/array.length) - X2);
        console.log("Dispersia=", dispersion);
        return dispersion;
      } else {
        Swal.fire({
          icon: 'error',
          title: 'Oops...',
          html: 'Nu ai destule elemente pentru a calcula dispersia <br> Media a fost calculata cu 1 element'
        });
      }
    },
    runThousandTimes() {
      for(let element= 0; element < ((Math.random() * 10000) +3000); element++) {
        this.Poisson();
      }
    }

  }, 
}
</script>

<style scoped>
h3 {
  margin: 30px 0 0;
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
  margin-top: 20px;
}

.el-input {
    width: 50%
}

.el-button {
    margin-top: 15px;
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
    height: 280px;
  }
</style>
