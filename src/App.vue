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
            <b-table bordered striped hover :items="candidatos"></b-table>
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
                                <b-form-input placeholder="Numero de Votos" type="number" min="0"></b-form-input>
                                <b-input-group-button>
                                    <b-btn variant="primary">Votar</b-btn>
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
                <b-table bordered striped hover :items="tokens"></b-table>
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
                    <b-form-input type="number" min="0"></b-form-input>
                    <b-input-group-button>
                      <b-btn variant="success">Comprar</b-btn>
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
export default {
  name: 'app',
  data () {
    return {
        candidatoSeleccionado: null,
        seleccionCandadatos: [
            { value: null, text: 'Selecciona tu candidato' },
            { value: 'Margarita Zavala', text: 'Margarita Zavala' },
            { value: 'Luis Videgaray', text: 'Luis Videgaray' },
            { value: 'Andres Manuel Lopez Obrador (PEJE)', text: 'Andres Manuel Lopez Obrador (PEJE)' },
        ],
        candidatos: [
            { nombre: 'Margarita Zavala', votos: 1 },
            { nombre: 'Luis Videgaray', votos: 4 },
            { nombre: 'Andres Manuel Lopez Obrador (PEJE)', votos: 3 },
        ],
        tokens: [
            { informacion: 'Votos a la venta', valor: 1 },
            { informacion: 'Total Votos Vendidos', valor: 4 },
            { informacion: 'Precio Por Voto', valor: 3 },
            { informacion: 'Total Dinero Recolectado', valor: 3 },
        ],
    }
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
