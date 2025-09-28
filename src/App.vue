<template>
  <div class="game">
    <h1 v-if="fase === 'setup'">L'IMPOSTORE</h1>

    <!-- Setup -->
    <div v-if="fase === 'setup'">
      <div v-if="numImpostori >= numGiocatori || numGiocatori <= 0 || numImpostori <= 0" class="error-msg">
        IL NUMERO DI IMPOSTORI DEVE ESSERE MINORE DI QUELLO DEI GIOCATORI
        <br />
        IL NUMERO DI IMPOSTORI E GICATORI NON PUO' ESSERE 0
      </div>
      <label>
        INSERISCI IL NUMERO DI GIOCATORI:
        <input type="number" v-model.number="numGiocatori" />
      </label>
      <br />
      <br />
      <label>
        INSERISCI IL NUMERO DI IMPOSTORI:
        <input type="number" v-model.number="numImpostori" />
      </label>
      <br />
      <br />
  <button class="btn-next" @click="iniziaNomi"
    :disabled="numImpostori >= numGiocatori || numGiocatori <= 0 || numImpostori <= 0">
    Avanti
  </button>
    </div>

    <!-- Inserimento nomi -->
    <div v-else-if="fase === 'nomi'">
      <div v-if="giocatori.some(nome => !nome)" class="error-msg">
        TUTTI DEVONO AVERE UN NOME
      </div>
      <h2>INSERISCI I NOMI</h2>
      <div v-for="(nome, i) in giocatori" :key="i">
        <label style="margin-right: 30px;">NOME GIOCATORE {{ i + 1 }}</label>
        <input v-model="giocatori[i]" :placeholder="'Nome giocatore ' + (i + 1)" />
      </div>
      <button class="btn-next" @click="iniziaGioco"
      :disabled="giocatori.some(nome => !nome)">Inizia Gioco</button>
    </div>

    <!-- Gioco -->
    <div v-else-if="fase === 'gioco'">
      <div v-if="!mostrata">
        <h2>{{ giocatori[turnoCorrente] }}</h2>
        <p>PREMI IL BOTTONE PER VEDERE LA PAROLA</p>
        <br />
        <button class="btn-next" @click="mostraParola">Mostra</button>
      </div>
      <div v-else>
        <h3 :class="parolaDaMostrare === 'SEI L\'IMPOSTORE!' ? 'impostore-msg' : ''">{{ parolaDaMostrare }}</h3>
        <br />
        <button class="btn-next" @click="prossimoGiocatore">Avanti</button>
      </div>
    </div>
    
    <!-- Aspettare la fine del turno-->
    <div v-else-if="fase === 'aspetta'">  
        <h2>PREMERE IL TASTO ALLA FINE DELLA PARTITA</h2>
        <button class="btn-next" @click="endGame">Fine partita</button>
    </div>

    <!-- Fine -->
    <div v-else-if="fase === 'fine'">
      <h2>PARTITA TERMINATA!</h2>
      <button class="btn-next" @click="ricomincia">Ricomincia</button>
    </div>

  </div>
</template>

<script>
export default {
  data() {
    return {
      fase: "setup",
      numGiocatori: 0,
      numImpostori: 0,
      giocatori: [],
      impostori: [],
      parole: [
        "MINATORE","ROMA","SEGNO ZODIACALE",
        "GROTTA","AEREOPORTO","PRADA","UNA NOTTE DA LEONI",
        "PARAFULMINE","PIGIAMA PARTY","FARE L'AUTOSTOP",
        "PARAOLIMPIADI","CUOCO","THE WOLF OF WALL STREET","DIO","GIN TONIC",
        "GHIACCIO","CAVATAPPI","GRAVITA'","POLPO","SCACCHI","CAFFE'","FARAONE",
        "ARCIERE","FANTASMA","SEMAFORO","FRITTO MISTO","IL RE LEONE","AVATAR",
        "JURASSIC PARK","COCA COLA","AMAZON","VAMPIRO","OMBRA","KEBAB",
        "BICICLETTA","SACCO A PELO","MOTO","TENNISTA","PIRATA","ZOMBIE","CLOWN",
        "BIRRA","PIZZA","CALCIO","BASKET","LISBONA","PING PONG","ACCENDINO","SIGARETTA","MADAGASCAR",
        "TITANIC","BANCOMAT","APE","CARTA IGENICA","DIPENDENZA","TALPA","QUALSIASI PAROLA CHE INIZIA CON N",
        "SPIDER-MAN","BESTEMMIA","LA FESSA","PADDLE","PONTE","PERDERE LA VERGINITA'","GLI EBREI",
        "IL PAPA","MAFIA","INCESTO","COMUNISMO","FASCISMO","TATUAGGIO","UN DITO","GIOCO D'AZZARDO"

      ],
      paroleUsate: [],
      parolaCorrente: "",
      parolaDaMostrare: "",
      turnoCorrente: 0,
      mostrata: false,
    };
  },
  methods: {
    iniziaNomi() {
      this.giocatori = Array(this.numGiocatori).fill("");
      this.fase = "nomi";
    },
    iniziaGioco() {
      // scegli impostori casuali
      let impostori = new Set();
      while (impostori.size < this.numImpostori) {
        impostori.add(Math.floor(Math.random() * this.numGiocatori));
      }
      this.impostori = [...impostori];

      this.nuovaParola();
      this.turnoCorrente = 0;
      this.fase = "gioco";
    },
    nuovaParola() {
      let scelta;
      do {
        scelta = Math.floor(Math.random() * this.parole.length);
      } while (this.paroleUsate.includes(scelta));
      this.paroleUsate.push(scelta);
      this.parolaCorrente = this.parole[scelta];
      this.parolaDaMostrare = this.parolaCorrente;
    },
    mostraParola() {
      if (this.impostori.includes(this.turnoCorrente)) {
        this.parolaDaMostrare = "SEI L'IMPOSTORE!";
      }
      this.mostrata = true;
    },
    prossimoGiocatore() {
      this.mostrata = false;
      this.turnoCorrente++;
      if(this.turnoCorrente < this.numGiocatori){

        this.parolaDaMostrare = this.parolaCorrente;
        this.fase = "gioco";
        
      }
      else{

        this.fase = "aspetta";
      }
    },
    endGame(){
      this.fase = "fine";
    },
    ricomincia() {
      this.mostrata = false;
      this.iniziaGioco();
    }
  }
};
</script>

<style>
.game {
  text-align: center;
  font-family: Arial, sans-serif;
}

.btn-next {
  background: #111;
  color: #fff;
  font-size: 1.3em;
  padding: 0.7em 2em;
  border: none;
  border-radius: 8px;
  margin-top: 18px;
  cursor: pointer;
  transition: background 0.2s;
}
  .btn-next:hover {
    background: #333;
  }

  .error-msg {
    color: #ff1a1a;
    font-weight: bold;
    margin-bottom: 20px;
    font-size: 1.1em;
  }

/* Nasconde le freccette dagli input number */
input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
input[type="number"] {
  -moz-appearance: textfield;
}
body {
  min-height: 100vh;
  background-color: #bdbdbd; /* grigio medio/scuro, opaco */
}

.impostore-msg {
  color: #ff1a1a;
  font-weight: bold;
}
</style>
