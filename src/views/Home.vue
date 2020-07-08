<template>
  <div class="home">
    <h1>New Place</h1>
    <ul>
      <li v-for="error in createErrors">{{ error }}</li>
    </ul>

    <div>
      Name: <input type="text" v-model="newPlaceName"><br>
      Address: <input type="text" v-model="newPlaceAddress"><br>
    <button v-on:click="createPlace()">Add Place</button>
    </div>

    <div v-for="place in places">
      <h2>{{ place.name }}</h2>
      <h3>{{ place.address }}</h3>
      <button v-on:click="showPlace(place)">More info</button>
    </div>
    
    <dialog id="place-details">
      <form method="dialog">
        <h1>Place Details</h1>
        <ul>
          <li v-for="error in updateErrors">{{ error }}</li>
        </ul>
        <p>Name:<input type ="text" v-model="currentPlace.name"></p>
        <p>Address:<input type="text" v-model="currentPlace.address"></p>
        <button v-on:click="updatePlace(currentPlace)">Update</button>
        <button v-on:click="destroyPlace(currentPlace)">Delete</button>
        <button>Close</button>
      </form>
    </dialog>

  </div>
</template>

<style>
</style>

<script>
import axios from "axios";

export default {
  data: function() {
    return {
      places: [],
      createErrors: [],
      updateErrors: [],
      newPlaceName: "",
      newPlaceAddress: "",
      currentPlace: {}
    };
  },
  created: function() {
    this.indexPlaces();
  },
  methods: {
    indexPlaces: function() {
      axios.get("/api/places").then(response => {
        console.log("All places:", response.data);
        this.places = response.data;
      });
    },
    createPlace: function() {
      var params = {
        name: this.newPlaceName,
        address: this.newPlaceAddress
      };
      axios
        .post("api/places", params)
        .then(response => {
          console.log("Success!", response.data);
          this.places.push(response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
      "form: input".attr("value", "");
    },
    showPlace: function(place) {
      console.log(place);
      document.querySelector("#place-details").showModal();
      this.currentPlace = place;
    },
    updateProPlaceduct: function(place) {
      var params = {
        name: place.name,
        address: place.address
      };
      axios
        .patch(`/api/places/${pleace.id}`, params)
        .then(response => {
          console.log("Successfully Updated", response.data);
        })
        .catch(error => {
          console.log(error.response.data.errors);
          this.updateErrors = error.response.data.errors;
        });
    },
    destroyPlace: function(place) {
      axios.delete(`/api/places/${place.id}`).then(response => {
        console.log("Successfully destroyed", response.data);
        var index = this.places.indexOf(place);
        this.places.splice(index, 1);
      });
    }
  }
};
</script>

