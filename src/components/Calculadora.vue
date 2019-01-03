<template>
  <div id="calculadora-pagina">
    <div class="calculadora">
      <div class="displayTipo">{{this.calculo.tipoDeCalculo}}</div>
      <div @click="mostraInfo()" class="info">Info</div>
      <div class="display">{{mostraEquacao(this.calculo.equacaoEmLista) || this.ultimoDaEquacao}}</div>
      <div @click="trocaTipo" class="botao">Tipo</div>
      <div @click="limpa" class="botao">C</div>
      <div @click="deleta" class="botao">DEL</div>
      <div @click="concatenaUltimoAEquacao('DIVISAO')" class="botao operator">÷</div>
      <div @click="concatena('7')" class="botao">7</div>
      <div @click="concatena('8')" class="botao">8</div>
      <div @click="concatena('9')" class="botao">9</div>
      <div @click="concatenaUltimoAEquacao('MULTIPLICACAO')" class="botao operator">x</div>
      <div @click="concatena('4')" class="botao">4</div>
      <div @click="concatena('5')" class="botao">5</div>
      <div @click="concatena('6')" class="botao">6</div>
      <div @click="concatenaUltimoAEquacao('SUBTRACAO')" class="botao operator">-</div>
      <div @click="concatena('1')" class="botao">1</div>
      <div @click="concatena('2')" class="botao">2</div>
      <div @click="concatena('3')" class="botao">3</div>
      <div @click="concatenaUltimoAEquacao('SOMA')" class="botao operator">+</div>
      <div @click="concatena('0')" class="botao zero">0</div>
      <div @click="ponto" class="botao">.</div>
      <div @click="salvaCalculo(calculo)" class="botao operator">=</div>
    </div>

    <div id="info">
      <p>Simples: Executa a equação da esquerda para direita.
        <br>Exemplo: 5 + 7 X 4 – 10 ÷ 2 = 19
      </p>
      <p>Científica: Executa na order das operações matemática
        <br>(multiplicacão e divisão primeiro).
        <br>Exemplo: 5 + 7 X 4 – 10 ÷ 2 = 28
      </p>
      
    <p>Veja o código fonte: </p>
    <p><a target="_blank" href="https://github.com/RickMineli/CalculadoraSimplesECientifica-Frontend">Front-end</a></p>
    <p><a target="_blank" href="https://github.com/RickMineli/CalculadoraSimplesECientifica">Back-end</a></p>

      <div @click="mostraCalculos()">Clique aqui para ver todos os calculos já feitos.</div>
      <div>
        <br>
        <div>
          <ul>
            <li v-for="calculo in calculos" :key="calculo.id">
              <br>
              Tipo: {{calculo.tipoDeCalculo}}
              <br>
              Equação: {{calculo.equacao}}
              <br>
              Resultado: {{calculo.resultado}}
              <br>
              Data: {{calculo.dataCalculo}}
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      operadorClicado: true,
      ultimoDaEquacao: "",
      calculo: {
        equacaoEmLista: [],
        tipoDeCalculo: "SIMPLES"
      },
      calculos: []
    };
  },
  methods: {
    mostraInfo() {
      var x = document.getElementById("info");
      if (x.style.display === "none") {
        x.style.display = "block";
      } else {
        x.style.display = "none";
      }
    },

    mostraEquacao(equacaoEmLista) {
      for (var i = 0; i < equacaoEmLista.length; i++) {
        equacaoEmLista[i] = equacaoEmLista[i].replace("SOMA", "+");
        equacaoEmLista[i] = equacaoEmLista[i].replace("SUBTRACAO", "-");
        equacaoEmLista[i] = equacaoEmLista[i].replace("MULTIPLICACAO", "x");
        equacaoEmLista[i] = equacaoEmLista[i].replace("DIVISAO", "÷");
      }
      return equacaoEmLista.join(" ") + " " + this.ultimoDaEquacao;
    },
    limpa() {
      this.calculo.equacaoEmLista = [];
      this.ultimoDaEquacao = "";
    },
    deleta() {
      this.ultimoDaEquacao = this.ultimoDaEquacao.slice(0, -1);
    },
    trocaTipo() {
      if (this.calculo.tipoDeCalculo === "SIMPLES") {
        this.calculo.tipoDeCalculo = "CIENTIFICA";
      } else {
        this.calculo.tipoDeCalculo = "SIMPLES";
      }
    },
    concatena(string) {
      if (this.operadorClicado) {
        this.ultimoDaEquacao = "";
        this.operadorClicado = false;
      }
      this.ultimoDaEquacao = `${this.ultimoDaEquacao}${string}`;
    },
    ponto() {
      if (this.ultimoDaEquacao.indexOf(".") === -1) {
        this.concatena(".");
      }
    },
    concatenaUltimoAEquacao(string) {
      if (this.ultimoDaEquacao !== "") {
        this.calculo.equacaoEmLista.push(this.ultimoDaEquacao);
        this.calculo.equacaoEmLista.push(string);
        this.ultimoDaEquacao = "";
      }
    },
    salvaCalculo(calculo) {
      if (this.ultimoDaEquacao !== "") {
        this.calculo.equacaoEmLista.push(this.ultimoDaEquacao);
        for (var i = 0; i < this.calculo.equacaoEmLista.length; i++) {
          this.calculo.equacaoEmLista[i] = this.calculo.equacaoEmLista[
            i
          ].replace("+", "SOMA");
          this.calculo.equacaoEmLista[i] = this.calculo.equacaoEmLista[
            i
          ].replace("-", "SUBTRACAO");
          this.calculo.equacaoEmLista[i] = this.calculo.equacaoEmLista[
            i
          ].replace("x", "MULTIPLICACAO");
          this.calculo.equacaoEmLista[i] = this.calculo.equacaoEmLista[
            i
          ].replace("÷", "DIVISAO");
        }
        fetch("https://calculadora-api-rickmineli.herokuapp.com/api/calculos", {
          body: JSON.stringify(calculo),
          method: "POST",
          headers: {
            Accept: "application/json",
            "Content-Type": "application/json"
          }
        })
          .then(response => response.json())
          .then(result => {
            this.limpa(), (this.ultimoDaEquacao = result.resultado.toString());
          });
      }
    },
    mostraCalculos() {
      fetch("https://calculadora-api-rickmineli.herokuapp.com/api/calculos")
        .then(response => response.json())
        .then(data => {
          this.calculos = data;
        });
    }
  }
};
</script>

<style scoped>
.calculadora {
  margin: 0 auto;
  width: 400px;
  font-size: 40px;
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-auto-rows: minmax(50px, auto);
  border: 1px solid orange;
}
.displayTipo {
  grid-column: 1 / 4;
  background-color: #333;
  color: white;
  font-size: 20px;
  text-align: left;
  padding-top: 15px;
  padding-left: 15px;
}
.info {
  background-color: #333;
  color: white;
  border-left: 1px solid orange;
  font-size: 30px;
  text-align: center;
  padding-top: 6px;
}
.display {
  grid-column: 1 / 5;
  background-color: #333;
  color: white;
  border: 1px solid orange;
}
.zero {
  grid-column: 1 / 3;
}
.botao {
  background-color: #f2f2f2;
  border: 1px solid grey;
}
.operator {
  background-color: orange;
  color: white;
}
#info {
  display: none;
}
li {
  list-style-type: none;
}
</style>
