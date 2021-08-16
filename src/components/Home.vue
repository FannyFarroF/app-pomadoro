<template>
  <div>
    <main>
      <div class="container py-5 px-0">
        <div class="">
          <h2 class="text-white text-center">Pomodoro</h2>
          <div class="options my-4">
            <ul class="p-0 m-0 d-flex justify-content-center">
              <li
                class="text-center item-opt px-2 py-3 d-flex justify-content-center align-items-center"
                v-for="item in options"
                :key="item.id"
                @click="seleccionarMet(item.id)"
                v-bind:class="item.state == true ? `color-${colorDefault}` : ''"
              >
                {{ item.name }}
              </li>
            </ul>
          </div>
          <div
            class="temp d-flex justify-content-center align-items-center position-relative"
          >
            <div class="content text-center w-100">
              <div class="pros">
                <p class="time d-flex justify-content-center text-white">
                  {{ min }} : {{ seg }}
                </p>
                <button class="btn" @click="start()" v-show="running == false">
                  Start
                </button>
                <button class="btn" @click="stop()" v-show="running">
                  Pause
                </button>
              </div>
            </div>
          </div>
          <div class="config d-flex justify-content-center">
            <button
              class="btn"
              data-bs-toggle="modal"
              data-bs-target="#exampleModal"
            >
              <span class="material-icons">
                settings
              </span>
            </button>
          </div>
          <!-- <div class="progress">
            <div
              class="progress-bar bg-success"
              role="progressbar"
              :style="'width:' + advanceTime + '%'"
              aria-valuenow="25"
              aria-valuemin="0"
              aria-valuemax="100"
            ></div>
          </div> -->
        </div>
      </div>
    </main>

    <!-- Modal -->
    <div
      class="modal fade"
      id="exampleModal"
      tabindex="-1"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog  modal-dialog-centered">
        <div class="modal-content position-relative">
          <hr class="line" :class="`color-${colorDefault}`" />
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">Settings</h5>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <div class="item py-3">
              <h6 class="text-start ">Times (Minutes)</h6>
              <div class="options-times d-flex justify-content-between">
                <div class="field text-start">
                  <label for="">Pomadoro</label>
                  <input
                    type="number"
                    name=""
                    id=""
                    min="1"
                    v-model="valuePom"
                  />
                </div>
                <div class="field text-start">
                  <label for="">Short break</label>
                  <input
                    type="number"
                    name=""
                    id=""
                    min="1"
                    v-model="valueSho"
                  />
                </div>
                <div class="field text-start">
                  <label for="">Long break</label>
                  <input
                    type="number"
                    name=""
                    id=""
                    min="1"
                    v-model="valueLon"
                  />
                </div>
              </div>
            </div>
            <div class="item py-3 d-flex justify-content-between">
              <h6 class="text-start">Color</h6>
              <div class="options-times  d-flex justify-content-between">
                <div class="field text-start d-flex ">
                  <button
                    class="btn d-flex justify-content-center"
                    v-for="color in colors"
                    :key="color.id"
                    :class="color.name"
                    @click="selectColor(color.id)"
                  >
                    <span class="material-icons" v-show="color.state == true">
                      check
                    </span>
                  </button>
                </div>
              </div>
            </div>
          </div>
          <div class="modal-footer w-100 d-flex justify-content-center">
            <button
              type="button"
              class="btn btn-primary py-2 px-3"
              @click="saveSettings()"
              :class="`color-${colorDefault}`"
            >
              Save changes
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  // import $ from "jquery";

  export default {
    name: "Test",
    created() {},
    data() {
      return {
        totalTime: null,
        advanceTime: 100,
        time: null,
        running: false,
        btnActive: false,
        options: [
          { id: 1, name: "Pomadoro", state: true, time: 25 },
          { id: 2, name: "Short break", state: false, time: null },
          { id: 3, name: "Long break", state: false, time: null },
        ],
        colors: [
          { id: 1, name: "color-1", state: true },
          { id: 2, name: "color-2", state: false },
          { id: 3, name: "color-3", state: false },
        ],
        optionDefault: 1,
        colorDefault: 1,
        colorTemporal: 1,
        valuePom: 25,
        valueSho: 5,
        valueLon: 15,
      };
    },
    props: {},
    mounted() {
      this.totalTime = this.options[0].time * 60;

      this.default();
    },
    watch: {
      optionDefault: function(value) {
        this.options.forEach((item) => {
          if (item.id == value) {
            item.state = true;
            this.totalTime = item.time * 60;
          } else {
            item.state = false;
          }
        });
      },
      colorTemporal: function(value) {
        this.colors.forEach((item) => {
          if (item.id == value) {
            item.state = true;
          } else {
            item.state = false;
          }
        });
      },
    },
    methods: {
      default() {
        this.options[0].time = this.valuePom;
        this.options[1].time = this.valueSho;
        this.options[2].time = this.valueLon;
      },

      seleccionarMet(value) {
        this.optionDefault = value;
        this.running = false;
        clearInterval(this.time);
        this.time = null;
      },
      selectColor(value) {
        this.colorTemporal = value;
      },
      saveSettings() {
        this.colorDefault = this.colorTemporal;

        // Guardar los nuevos valores

        this.default();

        window.$("#exampleModal .btn-close").click();
      },
      start() {
        this.running = true;

        if (this.running) {
          this.time = setInterval(() => {
            this.countDown();
          }, 1000);
        }
      },
      countDown() {
        if (this.totalTime > 0) {
          this.totalTime--;
          this.advanceTime = this.totalTime / 100;
        }
      },
      stop() {
        this.running = false;
        clearInterval(this.time);
      },
    },
    computed: {
      min: function() {
        const minutes = Math.floor(this.totalTime / 60);
        return minutes < 10 ? `0${minutes}` : `${minutes}`;
      },
      seg: function() {
        const segundes = this.totalTime - this.min * 60;
        return segundes < 10 ? `0${segundes}` : `${segundes}`;
      },
    },
  };
</script>

<style lang="scss">
  @import url("https://fonts.googleapis.com/css2?family=Crimson+Pro:wght@200;400;500;700;800;900&display=swap");
  @import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@100;300;400;500;700;800;900&display=swap");
  @import url("https://fonts.googleapis.com/css2?family=Lora:wght@500;600;700&display=swap");

  /* --------------------------------
           *** GENERAL ***
-----------------------------------*/

  :root {
    --primary-bg-color: #141e30;
    --secondary-bg-color: #243b55;
    --text-bg-color: #ffffff;
    --bg-color-uno: #f34612;
  }

  .color-1 {
    background: #f34612 !important;
    color: #fff !important;
  }
  .color-2 {
    background: #0cecdd !important;
    color: #000 !important;
  }
  .color-3 {
    background: #c400ff !important;
    color: #000 !important;
  }

  #app {
    background: var(--primary-bg-color);
    min-height: 100vh;
  }

  li {
    list-style: none;
  }

  a {
    text-decoration: none;
  }
  input {
    border: 2px solid transparent !important;
  }
  button:focus {
    box-shadow: none !important;
    border: none !important;
  }
  .color-txt-o {
    color: #ddd;
  }

  /* --------------------------------
           *** MAIN ***
-----------------------------------*/

  .options {
    width: max-content;
    margin: auto;
  }

  .options ul {
    width: max-content;
    background: #131b29;
    border-radius: 24px;
  }

  .options ul li {
    margin: 0 5px;
    transition: 0.8s all;
    width: 160px;
    cursor: pointer;
    color: #ccc;
    border-radius: 24px;
  }

  .options ul li.active {
    border-radius: 24px;
    color: #fff;
  }

  .temp {
    width: 250px;
    height: 250px;
    border-radius: 50%;
    background: #131b29;
    margin: 40px auto;
    box-shadow: -11px 11px 20px 6px;
    p {
      font-size: 3.5rem;
    }
    button {
      color: #ccc;
      letter-spacing: 0.1rem;
      outline: none;
      font-weight: 500;
      transition: 0.8s all;
    }
    button:hover {
      color: #bbb;
    }
  }
  .config span {
    color: #fff;
  }

  /* --------------------------------
           *** MODAL ***
-----------------------------------*/
  .modal-content {
    border-radius: 20px;
  }

  .modal .modal-header,
  .modal .modal-footer {
    border-top: 0 !important;
    border-bottom: 0 !important;
  }

  .modal-footer {
    position: absolute;
    bottom: -35px;
  }

  .modal-footer .btn {
    border: 0;
    border-radius: 15px;
  }

  .line {
    width: 90px;
    height: 10px !important;
    border: 0;
    margin: 0;
    position: absolute;
    left: 50px;
    top: -10px;
    border-radius: 10px 10px 0 0;
  }
  .modal-body {
    h6 {
      font-weight: 600;
      margin-top: 5px;
    }
    .item {
      border-top: 1px solid #efefef;
      .btn {
        width: 35px;
        height: 35px;
        border-radius: 50%;
        margin-left: 10px;
      }
    }
  }
  .options-times {
    .field {
      label {
      }
      input {
        width: 120px;
        font-weight: 600;
        color: #333;
        margin-top: 5px;
        border: 0;
        background: #ddd;
        padding: 5px 10px;
        border-radius: 5px;
      }
    }
  }

  @media only screen and (max-width: 540px) {
    .options {
      width: 100%;
      ul {
        width: 100%;
        li {
          width: 33.33%;
        }
      }
    }
  }
</style>
