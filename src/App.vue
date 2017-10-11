<template>
  <div id="app">
    <br>
        <div class="container">
        <div class="header clearfix">
        <h3 class="text-muted">Sistema de Votacion Mexico</h3>
        </div>

        <div class="jumbotron">
        <h1 class="display-3">Tu voto vale.</h1>
        <p class="lead">Este es un sistema de votacion. Usando technologia de blockchain para no confiar la verasidad de los votos a un tercero.</p>
        </div>

        <div class="row">
            <b-table bordered hover :items="candidatos"></b-table>
        </div>

        <br>
        <div class="row">
            <div class="col-md-12">
                <b-card title="Votar por Candidato">
                    <br>
                    <div class="row">
                        <div class="col-md-6">
                            <b-form-select v-model="candidatoSeleccionado" :options="seleccionCandadatos" class="mb-3">
                            </b-form-select>
                        </div>
                        <div class="col-md-6">
                            <b-input-group>
                                <b-form-input v-model="tokensVotar" placeholder="Numero de Votos" type="number" min="0"></b-form-input>
                                <b-input-group-button>
                                    <b-btn variant="primary" @click="votar">Votar</b-btn>
                                </b-input-group-button>
                            </b-input-group>
                        </div>

                    </div>
                </b-card>
            </div>
        </div>

        <div class="row marketing">
            <div class="col-md-6">
                <h4>Tokens de votacion</h4>
                <br>
                <b-table bordered hover :items="tokens"></b-table>
            </div>
            <div class="col-md-6">
                <br>
                <br>
                <b-card title="Comprar Votos">
                <p class="card-text">
                    Nunca dijimos que este sistema de votacion seria democratico.
                    Los votos se compran.
                </p>
                <b-input-group>
                    <b-form-input v-model="votosComprar" type="number" min="0"></b-form-input>
                    <b-input-group-button>
                      <b-btn variant="success" @click="comprar">Comprar</b-btn>
                    </b-input-group-button>
                  </b-input-group>
            </b-card>
            </div>
        </div>


        <br>
        <footer class="footer">
        <p>&copy; Company 2017</p>
        </footer>

    </div> <!-- /container -->
  </div>
</template>

<script>
// Import libraries we need.
import { default as Web3} from 'web3';
import { default as contract } from 'truffle-contract';

import voting_artifacts from '../build/contracts/Voting.json';



export default {
  name: 'app',
  data () {
        return {
            voting: null,
            candidatoSeleccionado: null,
            precioPorVoto: null,
            votosComprar: null,
            tokensVotar: null,

            seleccionCandadatos: [
                { value: null, text: 'Selecciona tu candidato' },
                { value: null, text: null },
                { value: null, text: null },
                { value: null, text: null },
            ],
            candidatos: [
                { nombre: null, votos: null },
                { nombre: null, votos: null },
                { nombre: null, votos: null },
            ],
            tokens: [
                { informacion: 'Votos en Existencia', valor: null },
                { informacion: 'Votos Vendidos', valor: null },
                { informacion: 'Precio Por Voto', valor: null },
                { informacion: 'Total Dinero Recolectado', valor: null },
            ],
        }
    },
    methods: {
        votar(){
            let vue = this;
            this.voting.deployed().then(function(contractInstance) {
                let instance = contractInstance;
                instance.voteForCandidate(vue.candidatoSeleccionado, parseInt(vue.tokensVotar), {gas: 140000, from: web3.eth.accounts[0]}).then(function() {
                    instance.totalVotesFor.call(vue.candidatoSeleccionado).then(function(votos) {
                        vue.candidatos[vue.candidatoSeleccionado].votos = votos;
                    });
                });
            });
        },
        comprar(){
            let vue = this;
            this.voting.deployed().then(function(contractInstance) {
                let instance = contractInstance;
                let precio = vue.votosComprar * vue.precioPorVoto;
                instance.buy({value: web3.toWei(precio, 'ether'), from: web3.eth.accounts[0]}).then(function(v) {

                    // Updatear total vendidos
                    vue.tokens[1].valor = parseInt(vue.tokens[1].valor) + parseInt(vue.votosComprar);
                    // Updatear el total balance de la cueta maestra
                    web3.eth.getBalance(instance.address, function(error, result) {
                        let balance = web3.fromWei(result.toString());
                        vue.tokens[3].valor = balance + " Ether";
                    });

                    vue.votosComprar = "";

                });
            });
        },
        agregarCandidatos(){
            let vue = this;
            this.voting.deployed().then(function(contractInstance) {
                let instance = contractInstance;
                instance.allCandidates.call().then(function(candidateArray) {
                    for(let i=0; i < candidateArray.length; i++) {
                        /* We store the candidate names as bytes32 on the blockchain. We use the
                         * handy toUtf8 method to convert from bytes32 to string
                         */
                        let nombre = web3.toUtf8(candidateArray[i]);
                        vue.candidatos[i].nombre = nombre;
                        instance.totalVotesFor.call(i).then(function(votos) {
                            vue.candidatos[i].votos = votos;
                        });

                        vue.seleccionCandadatos[i+1].text = nombre;
                        vue.seleccionCandadatos[i+1].value = i;
                    }
                });
            });
        },
        agregarInfoTokens(){
            let vue = this;
            this.voting.deployed().then(function(contractInstance) {
                contractInstance.totalTokens().then(function(v) {
                    vue.tokens[0].valor = v.toString();
                });
                contractInstance.tokensSold.call().then(function(v) {
                    vue.tokens[1].valor = v.toString();
                });
                contractInstance.tokenPrice().then(function(v) {
                    vue.precioPorVoto = parseFloat(web3.fromWei(v.toString()));
                    vue.tokens[2].valor = vue.precioPorVoto + " Ether";
                });
                web3.eth.getBalance(contractInstance.address, function(error, result) {
                    let balance = web3.fromWei(result.toString());
                    vue.tokens[3].valor = balance + " Ether";
                });
            });
        }
    },
    mounted(){
        this.voting = contract(voting_artifacts);
        if (typeof web3 !== 'undefined') {
            console.warn("Using web3 detected from external source like Metamask")
            // Use Mist/MetaMask's provider
            window.web3 = new Web3(web3.currentProvider);
        } else {
            console.warn("No web3 detected. Falling back to http://localhost:8545. You should remove this fallback when you deploy live, as it's inherently insecure. Consider switching to Metamask for development. More info here: http://truffleframework.com/tutorials/truffle-and-metamask");
            // fallback - use your fallback strategy (local node / hosted node + in-dapp id mgmt / fail)
            window.web3 = new Web3(new Web3.providers.HttpProvider("http://localhost:8545"));
        }
        this.voting.setProvider(web3.currentProvider);
        this.agregarCandidatos();
        this.agregarInfoTokens();
    }
}
</script>


<style scoped>
/* Space out content a bit */
body {
  padding-top: 1.5rem;
  padding-bottom: 1.5rem;
}

/* Everything but the jumbotron gets side spacing for mobile first views */
.header,
.marketing,
.footer {
  padding-right: 1rem;
  padding-left: 1rem;
}

/* Custom page header */
.header {
  padding-bottom: 1rem;
  border-bottom: .05rem solid #e5e5e5;
}
/* Make the masthead heading the same height as the navigation */
.header h3 {
  margin-top: 0;
  margin-bottom: 0;
  line-height: 3rem;
}

/* Custom page footer */
.footer {
  padding-top: 1.5rem;
  color: #777;
  border-top: .05rem solid #e5e5e5;
}

/* Customize container */
@media (min-width: 48em) {
  .container {
    max-width: 46rem;
  }
}
.container-narrow > hr {
  margin: 2rem 0;
}

/* Main marketing message and sign up button */
.jumbotron {
  text-align: center;
  border-bottom: .05rem solid #e5e5e5;
}
.jumbotron .btn {
  padding: .75rem 1.5rem;
  font-size: 1.5rem;
}

/* Supporting marketing content */
.marketing {
  margin: 3rem 0;
}
.marketing p + h4 {
  margin-top: 1.5rem;
}

/* Responsive: Portrait tablets and up */
@media screen and (min-width: 48em) {
  /* Remove the padding we set earlier */
  .header,
  .marketing,
  .footer {
    padding-right: 0;
    padding-left: 0;
  }
  /* Space out the masthead */
  .header {
    margin-bottom: 2rem;
  }
  /* Remove the bottom border on the jumbotron for visual effect */
  .jumbotron {
    border-bottom: 0;
  }
}
</style>
