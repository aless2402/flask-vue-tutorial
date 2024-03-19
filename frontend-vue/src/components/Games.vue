<template>
  <div class="jumbotron vertical-center">
    <div class="container">
      <!-- Bootswatch -->
      <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootswatch@4.5.2/dist/sketchy/bootstrap.min.css"
        integrity="sha384-RxqHG2ilm4r6aFRpGmBbGTjsqwfqHOKy1ArsMhHusnRO47jcGqpIQqlQK/kmGy9R" crossorigin="anonymous" />

      <div class="row">
        <div class="col-sm-12">
          <h1 class="text-center bg-primary text-white" style="border-radius: 10px">
            Games Library 🎮
          </h1>
          <hr /><br />

          <!-- Alert -->
          <div v-if="showMessage" class="alert alert-success">{{ message }}</div>
          <!-- Add Game button -->
          <button type="button" class="btn btn-success btn-sm" @click="showAddGameModal">
            Add Game
          </button>
          <br /><br />
          <!-- Add a bootstrap table -->
          <table class="table table-hover">
            <thead>
              <tr>
                <!-- table header cells -->
                <th scope="col">Title</th>
                <th scope="col">Genre</th>
                <th scope="col">Played</th>
                <th scope="col">Actions</th>
              </tr>
            </thead>
            <tbody>
              <!-- tr: table row -->
              <tr v-for="game in games" :key="game.id">
                <td>{{ game.title }}</td>
                <td>{{ game.genre }}</td>
                <td>
                  <span v-if="game.played">yes</span>
                  <span v-else>no</span>
                </td>
                <td>
                  <div class="btn-group" role="group">
                    <button type="button" class="btn btn-info btn-sm" @click="editGame(game)">update</button>
                    <button type="button" class="btn btn-danger btn-sm" @click="deleteGame(game)">delete</button>
                  </div>
                </td>
              </tr>
            </tbody>
          </table>

          <!-- Footer -->
          <footer class="bg-primary text-white text-center" style="border-radius: 10px; padding: 10px">
            Copyright &copy;. All Rights Reserved 2024.
          </footer>
        </div>
      </div>
      <!-- Modal -->
      <div id="game-modal" class="modal fade" ref="addGamesModal" tabindex="-1" aria-labelledby="exampleModalLabel"
        aria-hidden="true">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title" id="exampleModalLabel">Add a new game</h5>
              <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <form @submit.prevent="onSubmit" @reset.prevent="onReset">
              <div class="modal-body">
                <!-- form group for title entry -->
                <div class="mb-3">
                  <label for="form-title-input" class="form-label">Title:</label>
                  <input id="form-title-input" type="text" class="form-control" v-model="addGameForm.title" required
                    placeholder="Enter Game">
                </div>
                <!-- form group for genre entry -->
                <div class="mb-3">
                  <label for="form-genre-input" class="form-label">Genre:</label>
                  <input id="form-genre-input" type="text" class="form-control" v-model="addGameForm.genre" required
                    placeholder="Enter genre">
                </div>
                <!-- Checkbox -->
                <div class="mb-3 form-check d-flex justify-content-center align-items-center">
                  <input id="form-checks" type="checkbox" class="form-check-input" v-model="addGameForm.played">
                  <label class="form-check-label" for="form-checks">Played?</label>
                </div>
              </div>
              <div class="modal-footer">
                <button type="submit" class="btn btn-primary">Submit</button>
                <button type="reset" class="btn btn-secondary">Reset</button>
              </div>
            </form>
          </div>
        </div>
      </div>

      <!-- Modal Update -->
      <div id="game-update-modal" class="modal fade" ref="editGameModal" tabindex="-1"
        aria-labelledby="exampleModalLabel" aria-hidden="true">
        <div class="modal-dialog">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title" id="exampleModalLabel">Update Game</h5>
              <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
            </div>
            <form @submit.prevent="onSubmitUpdate" @reset.prevent="onResetUpdate">
              <div class="modal-body">
                <!-- form group for title entry -->
                <div class="mb-3">
                  <label for="form-title-edit-input" class="form-label">Title:</label>
                  <input id="form-title-edit-input" type="text" class="form-control" v-model="editForm.title" required
                    placeholder="Enter Game">
                </div>
                <!-- form group for genre entry -->
                <div class="mb-3">
                  <label for="form-genre-edit-input" class="form-label">Genre:</label>
                  <input id="form-genre-edit-input" type="text" class="form-control" v-model="editForm.genre" required
                    placeholder="Enter genre">
                </div>
                <!-- Checkbox -->
                <div class="mb-3 form-check d-flex justify-content-center align-items-center">
                  <input id="form-checks-edit" type="checkbox" class="form-check-input" v-model="editForm.played">
                  <label class="form-check-label" for="form-checks-edit">Played?</label>
                </div>
              </div>
              <div class="modal-footer">
                <button type="submit" class="btn btn-primary">Update</button>
                <button type="reset" class="btn btn-secondary">Cancel</button>
              </div>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import { Modal } from 'bootstrap'; //

export default {
  data() {
    return {
      games: [],
      addGameForm: {
        title: '',
        genre: '',
        played: [],
      },
      editForm: {
        id: '',
        title: '',
        genre: '',
        played: [],
      },
      showMessage: false,
      message: '',
    };
  },
  methods: {
    // Método para obtener los juegos
    getGames() {
      const path = 'http://localhost:5000/games';
      axios.get(path)
        .then((res) => {
          this.games = res.data.games;
        })
        .catch((error) => {
          console.error(error);
        });
    },
    // Método para agregar un juego
    addGame(payload) {
      const path = 'http://localhost:5000/games';
      axios.post(path, payload)
        .then(() => {
          this.getGames();
          this.message = 'Game added 🕹️!';
          this.showMessage = true;
        })
        .catch((error) => {
          console.error(error);
          this.getGames();
        });
    },
    // Método para restablecer los formularios
    initForm() {
      this.addGameForm.title = '';
      this.addGameForm.genre = '';
      this.addGameForm.played = [];
      this.editForm.id = '';
      this.editForm.title = '';
      this.editForm.genre = '';
      this.editForm.played = [];
    },
    // Método para manejar la presentación del modal de agregar juego
    showAddGameModal() {
      this.initForm();
      const modalElement = document.getElementById('game-modal');
      const modal = new Modal(modalElement);
      modal.show();
    },
    // Método para manejar la presentación del modal de actualizar juego
    editGame(game) {
      this.editForm = { ...game }; // Asegúrate de hacer una copia del objeto para evitar la modificación directa
      const modalElement = document.getElementById('game-update-modal');
      const modal = new Modal(modalElement);
      modal.show();
      this.message = ''; // Restablecer el mensaje después de abrir el modal de actualización
    },

    // Método para manejar la presentación del modal de agregar juego
    onSubmit(e) {
      e.preventDefault();
      const played = this.addGameForm.played[0] || false;
      const payload = {
        title: this.addGameForm.title,
        genre: this.addGameForm.genre,
        played,
      };
      this.addGame(payload);
      this.initForm();
      const modalElement = document.getElementById('game-modal');
      const modal = new Modal(modalElement);
      modal.hide();
    },
    // Método para manejar la presentación del modal de actualizar juego
    onSubmitUpdate(e) {
  e.preventDefault();
  // Determinar si el juego se ha jugado o no
  const played = this.editForm.played ? true : false;
  // Crear el payload con los datos actualizados
  const payload = {
    title: this.editForm.title,
    genre: this.editForm.genre,
    played,
  };
  // Enviar la solicitud de actualización al servidor con el ID del juego
  this.updateGame(payload, this.editForm.id)
    .then(() => {
      // Ocultar el modal después de una actualización exitosa
      if (this.$refs.editGameModal) {
        const modal = new Modal(this.$refs.editGameModal);
        modal.hide();
      }
      // Restablecer el formulario u otra lógica necesaria
      this.initForm();
    })
    .catch((error) => {
      console.error(error);
      // Manejar errores
    });
},
    // Método para manejar la presentación del modal de agregar juego
    onReset(e) {
      e.preventDefault();
      this.initForm();
      const modalElement = document.getElementById('game-modal');
      const modal = new Modal(modalElement);
      modal.hide();
    },
    // Método para manejar la presentación del modal de actualizar juego
    onResetUpdate(e) {
      e.preventDefault();
      this.initForm();
      const modalElement = document.getElementById('game-update-modal');
      const modal = new Modal(modalElement);
      modal.hide();
    },
    // Método para actualizar un juego
    updateGame(payload, gameID) {
      const path = `http://localhost:5000/games/${gameID}`;
      axios.put(path, payload)
        .then(() => {
          this.getGames();
          this.message = 'Game updated ⚙️!';
          this.showMessage = true;
          const modalElement = document.getElementById('game-update-modal');
          const modal = new Modal(modalElement);
          modal.hide(); // Cerrar el modal después de una actualización exitosa
        })
        .catch((error) => {
          console.error(error);
          this.getGames();
        });
    },
    // Método para eliminar un juego
    deleteGame(game) {
      const path = `http://localhost:5000/games/${game.id}`;
      axios.delete(path)
        .then(() => {
          this.getGames();
          this.message = 'Game Removed 🗑️!';
          this.showMessage = true;
        })
        .catch((error) => {
          console.error(error);
          this.getGames();
        });
    },

  },
  created() {
    this.getGames();
  },
};
</script>