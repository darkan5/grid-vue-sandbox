<template>
  <div id="app" style="width: 100%">
    <div>
      <div class="layoutJSON">
        <div class="center">{{ window.width }}</div>
        <div class="center">
          <div class="icon-holder" @click="previewChange" id="icon-1">
            <i class="fas fa-eye resize-icon"></i>
            <br />Pogląd
          </div>
          <div class="icon-holder" @click="changeResoltion('xxs')" id="icon-2">
            <i class="fas fa-mobile-alt resize-icon"></i>
            <br />0px-500px
          </div>
          <div class="icon-holder" @click="changeResoltion('xs')" id="icon-3">
            <i class="fas fa-tablet-alt resize-icon"></i>
            <br />500px-768px
          </div>
          <div class="icon-holder" @click="changeResoltion('sm')" id="icon-4">
            <i class="fas fa-tablet-alt resize-icon fa-rotate-90"></i>
            <br />768px-1024px
          </div>
          <div class="icon-holder" @click="changeResoltion('md')" id="icon-5">
            <i class="fas fa-laptop resize-icon"></i>
            <br />1024px-1280px
          </div>
          <div class="icon-holder" @click="changeResoltion('lg')" id="icon-6">
            <i class="fas fa-desktop resize-icon"></i>
            <br />>1280px
          </div>
          <div class="icon-holder" @click="changeResoltion('all')" id="icon-7">
            <i class="fas fa-arrows-alt-h resize-icon"></i>
            <br />100%
          </div>
        </div>
        <br />
      </div>
    </div>
    <button @click="saveLayers">Save</button>
    <button @click="testReponsiveLayout">Load</button>
    <button @click="clearTestLayout">Unload</button>
    <div class="flex">
      <div class="flex">
        <div
          v-for="gridLayoutLayer in gridLayoutLayers"
          :key="'keyBlank' + gridLayoutLayer.id"
        >
          <div :refenceGrid="gridLayoutLayer.id">
            <button
              :id="'text-button' + gridLayoutLayer.id"
              @click="selectComponentText(gridLayoutLayer.id)"
            >
              Text z tłem
            </button>
            <button @click="selectComponentCart">Karta</button>
            <button
              v-if="gridLayoutLayer.id != 0"
              :idRemove="gridLayoutLayer.id"
              @click="removeGrid(gridLayoutLayer.id)"
            >
              -
            </button>
            <button
              v-if="gridLayoutLayer.id != 0"
              :idRemove="gridLayoutLayer.id"
              @click="upGrid(gridLayoutLayer.id)"
            >
              up
            </button>
            <button
              v-if="gridLayoutLayer.id != gridLayoutLayers.length - 1"
              :idRemove="gridLayoutLayer.id"
              @click="downGrid(gridLayoutLayer.id)"
            >
              down
            </button>
            <div class="bodyContent">
              <div id="content" class="content" v-bind:class="reposiveCss">
                <grid-layout
                  :isDraggable="fullMode"
                  :key="gridLayoutLayer.message"
                  ref="gridLayout"
                  :layout.sync="
                    layoutsAll[gridLayoutLayer.id] == undefined
                      ? []
                      : layoutsAll[gridLayoutLayer.id]
                  "
                  :col-num="12"
                  :row-height="30"
                  :is-draggable="draggable"
                  :masterLayouts="
                    masterLayout[gridLayoutLayer.id] == undefined
                      ? {}
                      : masterLayout[gridLayoutLayer.id]
                  "
                  :is-resizable="true"
                  :vertical-compact="true"
                  :use-css-transforms="true"
                  :responsive="responsive"
                  :cols="{ lg: 12, md: 12, sm: 12, xs: 12, xxs: 12 }"
                  @layoutUpdatedEvent="layoutUpdatedEvent"
                  @breakpoint-changed="breakpointChangedEvent"
                >
                  <grid-item
                    v-for="item in layoutsAll[gridLayoutLayer.id]"
                    :x="item.x"
                    :y="item.y"
                    :w="item.w"
                    :h="item.h"
                    :i="item.i"
                    @resize="resizeEvent"
                    @move="moveEvent"
                    @resized="resizedEvent"
                    @moved="movedEvent"
                    :key="'k' + gridLayoutLayer.id"
                  >
                    <div
                      :refenceIteam="item.i"
                      v-if="item.type == 'text'"
                      style="display: contents"
                    >
                      <div
                        :class="item.font"
                        :style="{
                          color: item.color,
                          backgroundColor: item.bgcolor,
                          overflow: 'hidden',
                        }"
                      >
                        <div
                          :refenceIteam="item.i"
                          v-if="item.background != ''"
                          :style="{
                            backgroundImage:
                              'url(' + env + '/images/' + item.background + ')',
                            width: '100%',
                            height: '100%',
                            backgroundSize: item.backgroundSize,
                            backgroundRepeat: item.backgroundRepeat,
                          }"
                        >
                          {{ item.body }}
                        </div>
                        <div
                          :refenceIteam="item.i"
                          v-if="item.background == ''"
                        >
                          {{ item.body }}
                        </div>
                      </div>
                      <i
                        v-on:click="editComponent(item.i)"
                        v-if="item.editable"
                        class="fas fa-pencil-alt pencil"
                      ></i>
                      <i
                        v-on:click="deleteIteam(item.i)"
                        :id="gridLayoutLayer.id + '-' + item.i"
                        class="fas fa-trash trash"
                      ></i>
                    </div>
                  </grid-item>
                </grid-layout>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- <div v-bind:class="reposiveCss" :v-if="fullMode != true">
        <grid-layout :v-if="fullMode != true"
              :isDraggable="true"
              ref="gridLayout"
              :layout="layoutsAll[0] == undefined ? [] : layoutsAll[0]"
              :col-num="12"
              :row-height="30"
              :is-draggable="draggable"
              :is-resizable="true"
              :vertical-compact="true"
              :masterLayouts="masterLayout[0] == undefined ? {}: masterLayout[0]"
              :use-css-transforms="true"
              :responsive="responsive"
              :cols="{lg: 12, md: 12, sm: 12, xs: 12, xxs: 12}"
              @layoutUpdatedEvent="layoutUpdatedEvent"
              @breakpoint-changed="breakpointChangedEvent"
            >
              <grid-item
                v-for="item in layoutsAll[0]"
                :x="item.x"
                :y="item.y"
                :w="item.w"
                :h="item.h"
                :i="item.i"
                @resize="resizeEvent"
                @move="moveEvent"
                @resized="resizedEvent"
                @moved="movedEvent"
                :key="'c'+item.i"
              >
                <div :refenceIteam="item.i" v-if="item.type =='text'" style="display: contents">
                  <div
                    :class="item.font"
                    :style="{color: item.color ,
                    backgroundColor: item.bgcolor,
                    overflow:'hidden'
                   }"
                  >
                    <div
                      :refenceIteam="item.i"
                      v-if="item.background != ''"
                      :style="{backgroundImage: 'url('+env+'/images/'+  item.background + ')',
                            width: '100%' ,
                            height: '100%',
                            backgroundSize: item.backgroundSize,
                            backgroundRepeat: item.backgroundRepeat}"
                    >{{item.body}}</div>
                    <div :refenceIteam="item.i" v-if="item.background == ''">{{item.body}}</div>
                  </div>
                  <i
                    v-on:click="editComponent(item.i)"
                    v-if="item.editable"
                    class="fas fa-pencil-alt pencil"
                  ></i>
                  <i
                    v-on:click="deleteIteam(item.i)"
                    :id="'1-'+item.i"
                    class="fas fa-trash trash"
                  ></i>          </div>
              </grid-item>
            </grid-layout>
    </div> -->
    </div>
    <button @click="addGrid">+</button>

    <modal name="TextComponentModal" :width="1200" :height="850">
      <TextComponent
        @insertComponent="insertTextComponent"
        :componentIdNew="componentIdNew"
        :editValue="editComponentValue"
        :editView="reposiveCss"
        :editGrid="editGridValue"
        @editComponent="editTextComponent"
      ></TextComponent>
    </modal>
    <modal name="CartComponentModal" :width="1200" :height="850">
      <CartComponent
        @insertComponent="insertCartComponent"
        :editValue="editComponentValue"
        :editView="reposiveCss"
        @editComponent="editCartComponent"
      ></CartComponent>
    </modal>
  </div>
</template>

<script>
//var testcopyLayout = {"lg":[{"x":0,"y":5,"w":12,"h":2,"i":"0","componentName":"image","moved":false},{"x":2,"y":0,"w":2,"h":4,"i":"1","moved":false},{"x":4,"y":0,"w":2,"h":5,"i":"2","moved":false},{"x":6,"y":0,"w":2,"h":3,"i":"3","moved":false},{"x":8,"y":0,"w":2,"h":3,"i":"4","moved":false},{"x":8,"y":0,"w":2,"h":3,"i":"5","moved":false}],"xxs":[{"x":0,"y":4,"w":12,"h":2,"i":"0","componentName":"image","moved":false},{"x":2,"y":0,"w":2,"h":4,"i":"1","moved":false},{"x":7,"y":6,"w":2,"h":5,"i":"2","moved":false},{"x":6,"y":0,"w":2,"h":3,"i":"3","moved":false},{"x":5,"y":6,"w":2,"h":3,"i":"4","moved":false}],"xs":[{"x":0,"y":5,"w":12,"h":2,"i":"0","componentName":"image","moved":false},{"x":2,"y":0,"w":2,"h":4,"i":"1","moved":false},{"x":4,"y":0,"w":2,"h":5,"i":"2","moved":false},{"x":6,"y":0,"w":2,"h":3,"i":"3","moved":false},{"x":8,"y":0,"w":2,"h":3,"i":"4","moved":false}],"sm":[{"x":0,"y":5,"w":12,"h":2,"i":"0","componentName":"image","moved":false},{"x":2,"y":0,"w":2,"h":4,"i":"1","moved":false},{"x":4,"y":0,"w":2,"h":5,"i":"2","moved":false},{"x":6,"y":0,"w":2,"h":3,"i":"3","moved":false},{"x":8,"y":0,"w":2,"h":3,"i":"4","moved":false}],"md":[{"x":0,"y":5,"w":12,"h":2,"i":"0","componentName":"image","moved":false},{"x":2,"y":0,"w":2,"h":4,"i":"1","moved":false},{"x":4,"y":0,"w":2,"h":5,"i":"2","moved":false},{"x":6,"y":0,"w":2,"h":3,"i":"3","moved":false},{"x":8,"y":0,"w":2,"h":3,"i":"4","moved":false}]};
import Vue from "vue";
import { BootstrapVue, IconsPlugin } from "bootstrap-vue";

// Install BootstrapVue
Vue.use(BootstrapVue);
// Optionally install the BootstrapVue icon components plugin
Vue.use(IconsPlugin);
import axios from "axios";
import { GridLayout, GridItem } from "vue-grid-layout";
import VueModal from "vue-js-modal";
import { Swatches } from "vue-color";
import VueLodash from "vue-lodash";
import lodash from "lodash";
import CustomGridLayout from "./components/extendet/CustomGridLayout";
//  import vuetify from 'vuetify';
import TextComponent from "./components/modalComponent/TextComponent";
import CartComponent from "./components/modalComponent/CartComponent";
import "bootstrap/dist/css/bootstrap.css";
import "bootstrap-vue/dist/bootstrap-vue.css";
//var GridLayout = CustomGridLayout.GridLayout;
//var GridItem = CustomGridLayout.GridItem;
Vue.use(VueModal, {
  dynamic: true,
  dynamicDefaults: {
    clickToClose: false,
  },
  GridLayout,
  GridItem,
});
Vue.use(VueLodash, {
  name: "custom",
  lodash: lodash,
});
export default {
  name: "App",
  renderComponent: true,
  components: {
    GridLayout: CustomGridLayout,
    GridItem: GridItem,
    TextComponent,
    CartComponent,
    "swatches-picker": Swatches,
    VueModal,
  },
  data() {
    return {
      layouts: {},
      layout: [],
      masterLayout: [],
      zmienna: "nazwa",
      layoutsAll: [],
      fullMode: true,
      renderComponent: true,
      componentIdNew: 0,
      saveDataLayersToBase: [],
      getDataLayersFromDataBase: [],
      gridLayoutLayers: [
        {
          message: "gridLayout0",
          id: "0",
        },
        {
          message: "gridLayout1",
          id: "1",
        },
        {
          message: "gridLayout2",
          id: "2",
        },
        {
          message: "gridLayout3",
          id: "3",
        },
      ],
      editComponentValue: {},
      draggable: true,
      testowa: {},
      editGridValue: 0,
      env: "",
      breakpoint: "lg",
      componentKey: 0,
      preview: false,
      reposiveCss: "lg",
      index: 0,
      componentId: {},
      responsive: true,
      eventLog: [],
      window: {
        width: 0,
        height: 0,
      },
    };
  },
  mounted() {
    this.env = process.env.MIX_ROOT_API;
  },

  destroyed() {
    window.removeEventListener("resize", this.handleResize);
  },
  watch: {
    preview: function () {
      if (this.preview) {
        $(".vue-grid-item").css("border", "0px solid black");
        $(".vue-grid-item").css("background", "transparent");
        $(".vue-resizable-handle").css("display", "none");
        $(".trash").css("display", "none");
        $(".pencil").css("display", "none");
        $(".vue-grid-layout").css("background", "transparent");
        this.draggable = false;
        $("#icon-1").css("color", "red");
      } else {
        $(".vue-grid-item").css("border", "1px solid black");
        $(".vue-grid-item").css("background", "#FFF");
        $(".vue-grid-layout").css("background", "#EEE");
        $(".vue-resizable-handle").css("display", "block");
        $(".trash").css("display", "block");
        $(".pencil").css("display", "block");
        this.draggable = true;
        $("#icon-1").css("color", "black");
      }
    },
    masterLayout: function () {
      for (let index = 0; index < this.gridLayoutLayers.length; index++) {
        console.log("master");
        console.log(this.masterLayout[index][this.breakpoint]);
        this.$set(
          this.layoutsAll,
          index,
          this.masterLayout[index][this.breakpoint]
        );
      }
    },
  },
  computed: {},
  beforeCreate: function () {},
  created: function () {
    this.$root.log = function log() {
      for (let i = 0; i < arguments.length; i += 1) {
        if (typeof arguments[i] === "object") {
          try {
            arguments[i] = JSON.parse(JSON.stringify(arguments[i]));
          } catch (e) {
            console.error(e);
          }
        }
      }
      console.log(...arguments);
    };
    this.getStart();
  },
  methods: {
    addGrid: function () {
      let maxId = 0;
      for (let index = 0; index < this.gridLayoutLayers.length; index++) {
        if (this.gridLayoutLayers[index].id > maxId) {
          maxId = this.gridLayoutLayers[index].id;
        }
      }

      console.log("=== addGrid START ===");

      let numberOfNewGridLayout = maxId + 1;
      let numberOfNewGridId = maxId + 1;
      this.gridLayoutLayers.push({
        message: "gridLayout" + numberOfNewGridLayout,
        id: numberOfNewGridId,
      });

      console.log("this.gridLayoutLayers:");
      console.log(this.gridLayoutLayers);
      console.log("numberOfNewGridId:");
      console.log(numberOfNewGridId);
      console.log("=== addGrid END ===");
    },
    removeGrid: function (removeGridLayerId) {
      console.log("this.layoutsAll");
      console.log(this.layoutsAll);
      let removeGridLayer = parseInt(removeGridLayerId);
      console.log("===removeGridLayer====");
      console.log(removeGridLayer);

      this.gridLayoutLayers.splice(removeGridLayer, 1);

      for (let index = 0; index < this.gridLayoutLayers.length; index++) {
        this.gridLayoutLayers[index].id = index;
      }

      this.layoutsAll.splice(removeGridLayer, 1);
      console.log("=== removeGrid START ===");
      console.log("this.layoutsAll PO");
      console.log(this.layoutsAll);
      console.log("this.gridLayoutLayers");
      console.log(this.gridLayoutLayers);
      console.log("removeGridLayerId");
      console.log(removeGridLayerId);

      console.log("this.$refs.gridLayout[3].layouts");
      console.log(this.$refs.gridLayout[2].layouts);
      console.log("this.layoutsAll[2]");
      console.log(this.layoutsAll[2]);

      // this.$refs.gridLayout[2].layouts = this.layoutsAll[2];
      // this.$refs.gridLayout[2].onWindowResize();
      console.log("=== removeGrid END ===");
    },
    moveArray: function (arr, old_index, new_index) {
      console.log("===run function===");
      if (new_index >= arr.length) {
        var k = new_index - arr.length + 1;
        while (k--) {
          arr.push(undefined);
        }
      }
      arr.splice(new_index, 0, arr.splice(old_index, 1)[0]);
      return arr; // for testing
    },
    upGrid: function (layerId) {
      console.log("===up layerId ===");
      console.log(layerId);
      this.moveArray(this.masterLayout, layerId, layerId - 1);
    },

    downGrid: function (layerId) {
      console.log("===down layerId ===");
      console.log(layerId);
      this.moveArray(this.masterLayout, layerId, layerId + 1);
      for (let index = 0; index < this.gridLayoutLayers.length; index++) {
        this.gridLayoutLayers[index].id = index;
      }
    },

    testReponsiveLayout: function () {
      this.layoutsAll = [
        [
          {
            font: "big1",
            body: "1",
            color: "#000",
            bgcolor: "#FFF",
            background: "",
            backgroundSize: "auto",
            backgroundRepeat: "no-repeat",
            type: "text",
            i: 0,
            x: 1,
            y: 0,
            w: 5,
            h: 2,
            editable: true,
            moved: false,
          },
        ],
        [
          {
            font: "big1",
            body: "2",
            color: "#000",
            bgcolor: "#FFF",
            background: "",
            backgroundSize: "auto",
            backgroundRepeat: "no-repeat",
            type: "text",
            i: 1,
            x: 1,
            y: 0,
            w: 5,
            h: 2,
            editable: true,
            moved: false,
          },
        ],
        [
          {
            font: "big1",
            body: "3",
            color: "#000",
            bgcolor: "#FFF",
            background: "",
            backgroundSize: "auto",
            backgroundRepeat: "no-repeat",
            type: "text",
            i: 2,
            x: 1,
            y: 0,
            w: 5,
            h: 2,
            editable: true,
            moved: false,
          },
        ],
        [
          {
            font: "big1",
            body: "4",
            color: "#000",
            bgcolor: "#FFF",
            background: "",
            backgroundSize: "auto",
            backgroundRepeat: "no-repeat",
            type: "text",
            i: 3,
            x: 1,
            y: 0,
            w: 5,
            h: 2,
            editable: true,
            moved: false,
          },
        ],
      ];
      this.componentIdNew = 4;
    },
    clearTestLayout: function () {
      this.masterLayout = {};
      this.layoutsAll = [];
    },
    saveLayers: function () {
      this.saveDataLayersToDatabase = [];
      for (const value in this.gridLayoutLayers) {
        let valuePlus = parseInt(value);
        // console.log('this.gridLayout[valuePlus].layouts ', JSON.stringify(this.$refs.gridLayout[valuePlus].layouts));
        this.saveDataLayersToDatabase.push(
          JSON.stringify(this.$refs.gridLayout[valuePlus].layouts)
        );
      }

      // console.log('this.saveDataLayersToDatabase', this.saveDataLayersToDatabase);
      this.sendDataToDatabase();
    },
    copyValue: function (grid, gridElement) {
      this.$refs.gridLayout[gridElement].layouts = this.layouts[grid];
      this.$refs.gridLayout[gridElement].onWindowResize();
    },
    editComponent: function (arg) {
      const resolution = ["lg", "md", "sm", "xs", "xxs"];
      let edit = [];
      for (let v = 0; v < this.gridLayoutLayers.length; v++) {
        // for (const [key, value] of Object.entries(resolution)) {
        if (this.layoutsAll[v] != undefined) {
          edit[v] = this.layoutsAll[v].filter(function (obj) {
            return obj.i == arg;
          });
        }
      }

      // console.log('edit[v]', edit);
      for (let z = 0; z < this.gridLayoutLayers.length; z++) {
        if (edit[z] != undefined) {
          this.editComponentValue = edit[z];
        }
      }

      this.$modal.show("TextComponentModal");
    },
    breakpointChangedEvent: function (newBreakpoint, newLayout) {
      console.log(
        "BREAKPOINT CHANGED breakpoint=",
        newBreakpoint,
        ", layout: ",
        newLayout
      );
      this.breakpoint = newBreakpoint;
      //   if (this.masterLayout[0][newBreakpoint] != undefined) {
      //       this.layoutsAll = this.masterLayout[0][newBreakpoint] ;
      //       this.masterLayout[0][newBreakpoint]
      //   }
      //   for (let c = 0; c < this.gridLayoutLayers.length; c++) {
      //     if (
      //       this.masterLayout !== undefined &&
      //       this.masterLayout[c] !== undefined &&
      //       this.masterLayout[c][newBreakpoint] !== undefined
      //     ) {
      //       //this.layoutsAll[c].slice();

      //       this.$set(this.layoutsAll, c, this.masterLayout[c][newBreakpoint]);
      //       //this.layoutsAll[c] = this.masterLayout[c][newBreakpoint] ;
      //     }
      //   }

      //     if (newBreakpoint == 'xs'){
      //    this.layoutsAll[0] = this.masterLayout[2]['xs'] ;
      //    this.layoutsAll[1] = this.masterLayout[2]['xs'] ;
      //    this.layoutsAll[2] = this.masterLayout[2]['xs'] ;

      //      console.log(this.layoutsAll[2])

      // }

      //console.log('this.layoutsAll ', this.layoutsAll);

      //  console.log("master", this.masterLayout[0][newBreakpoint]);
    },
    previewChange: function () {
      this.preview = !this.preview;
    },
    selectComponentText: function (target) {
      this.editGridValue = target;
      this.$modal.show("TextComponentModal");

      this.editComponentValue = {
        lg: [
          {
            body: "Text domyślny",
            font: "big1",
            color: "#000",
            bgcolor: "#FFF",
            backgroundSize: "auto",
            backgroundRepeat: "no-repeat",
            background: "",
          },
        ],
        md: [
          {
            body: "Text domyślny",
            font: "big1",
            color: "#000",
            bgcolor: "#FFF",
            backgroundSize: "auto",
            backgroundRepeat: "no-repeat",
            background: "",
          },
        ],
        sm: [
          {
            body: "Text domyślny",
            font: "big1",
            color: "#000",
            bgcolor: "#FFF",
            backgroundSize: "auto",
            backgroundRepeat: "no-repeat",
            background: "",
          },
        ],
        xs: [
          {
            body: "Text domyślny",
            font: "big1",
            color: "#000",
            bgcolor: "#FFF",
            backgroundSize: "auto",
            backgroundRepeat: "no-repeat",
            background: "",
          },
        ],
        xxs: [
          {
            body: "Text domyślny",
            font: "big1",
            color: "#000",
            bgcolor: "#FFF",
            backgroundSize: "auto",
            backgroundRepeat: "no-repeat",
            background: "",
          },
        ],
      };
    },
    selectComponentCart: function () {
      this.$modal.show("CartComponent");
      this.editComponentValue = {
        lg: [
          {
            body: "Text domyślny",
            font: "big1",
            color: "#000",
            bgcolor: "#FFF",
            backgroundSize: "auto",
            backgroundRepeat: "no-repeat",
            background: "",
          },
        ],
        md: [
          {
            body: "Text domyślny",
            font: "big1",
            color: "#000",
            bgcolor: "#FFF",
            backgroundSize: "auto",
            backgroundRepeat: "no-repeat",
            background: "",
          },
        ],
        sm: [
          {
            body: "Text domyślny",
            font: "big1",
            color: "#000",
            bgcolor: "#FFF",
            backgroundSize: "auto",
            backgroundRepeat: "no-repeat",
            background: "",
          },
        ],
        xs: [
          {
            body: "Text domyślny",
            font: "big1",
            color: "#000",
            bgcolor: "#FFF",
            backgroundSize: "auto",
            backgroundRepeat: "no-repeat",
            background: "",
          },
        ],
        xxs: [
          {
            body: "Text domyślny",
            font: "big1",
            color: "#000",
            bgcolor: "#FFF",
            backgroundSize: "auto",
            backgroundRepeat: "no-repeat",
            background: "",
          },
        ],
      };
    },
    insertTextComponent: function (value, grid) {
      this.$modal.hide("TextComponentModal");
      //console.log("elementText",value);
      this.AddComponent(value, grid);
    },
    insertCartComponent: function (value) {
      this.$modal.hide("CartComponent");
      this.testowa = value;
      this.AddComponent(value);
    },
    editTextComponent: function (value) {
      const resolution = ["lg", "md", "sm", "xs", "xxs"];
      var change = {};
      for (var c = 0; c < resolution.length; c++) {
        change = _.find(this.layouts[resolution[c]], function (o) {
          return o.i === value[resolution[c]][0].i;
        });
        for (const [key, values] of Object.entries(change)) {
          change[key] = value[resolution[c]][0][key];
        }
      }
      // this.$refs.gridLayout.layouts = this.layouts;
      // this.$refs.gridLayout.onWindowResize();
      // this.copyValue();
      this.$modal.hide("TextComponent");
      this.check();
    },
    editCartComponent: function (value) {
      const resolution = ["lg", "md", "sm", "xs", "xxs"];
      var change = {};
      for (var c = 0; c < 5; c++) {
        change = _.find(this.layouts[resolution[c]], function (o) {
          return o.i === value[resolution[c]][0].i;
        });
        for (const [key, values] of Object.entries(change)) {
          change[key] = value[resolution[c]][0][key];
        }
      }
      this.$refs.gridLayout.layouts = this.layouts;
      this.$refs.gridLayout.onWindowResize();
      this.copyValue();
      this.$modal.hide("CartComponent");
      this.check();
    },
    changeResoltion: function (arg) {
      this.reposiveCss = arg;
      this.window.width = document.body.clientWidth;
    },
    getStart: function () {
      // console.log("getStart");
      axios
        .get(`http://quizkreator.local/cms/data/`)
        .then((response) => {
          // JSON responses are automatically parsed.
          if (response.data != "") {
            // //console.log(response.data);
            let dataFromDatabase = response.data;

            let maxx = 0;

            dataFromDatabase.forEach((element) => {
              //console.log("el:",element.orderId);
              this.masterLayout[element.orderId] = JSON.parse(element["body"]);
              // this.$refs.gridLayout[element.orderId].layouts = [] ;
              this.$refs.gridLayout[element.orderId].layouts["xxs"] = [];
              // this.$set(this.$refs.gridLayout[element.orderId].layouts,"xxs",(JSON.parse(element["body"].xxs))) ;
              //this.$refs.gridLayout[0].layouts.push({"lg":[{"font":"big1","body":"2","color":"#000","bgcolor":"#FFF","background":"","backgroundSize":"auto","backgroundRepeat":"no-repeat","type":"text","i":1,"x":1,"y":0,"w":5,"h":2,"editable":true,"moved":false},{"font":"big1","body":"Text domyślny","color":"#000","bgcolor":"#FFF","background":"","backgroundSize":"auto","backgroundRepeat":"no-repeat","type":"text","i":4,"x":6,"y":0,"w":5,"h":2,"editable":true,"moved":false}],"xs":[{"font":"big1","body":"2","color":"#000","bgcolor":"#FFF","background":"","backgroundSize":"auto","backgroundRepeat":"no-repeat","type":"text","i":1,"x":1,"y":0,"w":5,"h":2,"editable":true,"moved":false},{"font":"big1","body":"Text domyślny","color":"#000","bgcolor":"#FFF","background":"","backgroundSize":"auto","backgroundRepeat":"no-repeat","type":"text","i":4,"x":6,"y":0,"w":5,"h":2,"editable":true,"moved":false}],"xxs":[{"font":"big1","body":"5","color":"#000","bgcolor":"#FFF","background":"","backgroundSize":"auto","backgroundRepeat":"no-repeat","type":"text","i":1,"x":1,"y":0,"w":5,"h":7,"editable":true,"moved":false},{"font":"big1","body":"Text domyślny","color":"#000","bgcolor":"#FFF","background":"","backgroundSize":"auto","backgroundRepeat":"no-repeat","type":"text","i":4,"x":6,"y":0,"w":5,"h":2,"editable":true,"moved":false}],"sm":[{"font":"big1","body":"2","color":"#000","bgcolor":"#FFF","background":"","backgroundSize":"auto","backgroundRepeat":"no-repeat","type":"text","i":1,"x":1,"y":0,"w":5,"h":2,"editable":true,"moved":false},{"font":"big1","body":"Text domyślny","color":"#000","bgcolor":"#FFF","background":"","backgroundSize":"auto","backgroundRepeat":"no-repeat","type":"text","i":4,"x":6,"y":0,"w":5,"h":2,"editable":true,"moved":false}],"md":[{"font":"big1","body":"2","color":"#000","bgcolor":"#FFF","background":"","backgroundSize":"auto","backgroundRepeat":"no-repeat","type":"text","i":1,"x":1,"y":0,"w":5,"h":2,"editable":true,"moved":false},{"font":"big1","body":"Text domyślny","color":"#000","bgcolor":"#FFF","background":"","backgroundSize":"auto","backgroundRepeat":"no-repeat","type":"text","i":4,"x":6,"y":0,"w":5,"h":2,"editable":true,"moved":false}]});
              //console.log(JSON.parse(element["body"]).lg);
              this.layoutsAll[element.orderId] = JSON.parse(element["body"]).lg;

              this.$refs.gridLayout[element.orderId].layouts["xxs"] = ["b"];
              for (
                let index = 0;
                index < JSON.parse(element["body"]).lg.length;
                index++
              ) {
                if (JSON.parse(element["body"]).lg[index].i > maxx) {
                  maxx = JSON.parse(element["body"]).lg[index].i;
                }
              }
              this.componentIdNew = maxx + 1;
              // console.log("getStart maxx:  ", maxx);
              // console.log('getStart this.componentIdNew', this.componentIdNew);
            });

            this.$forceUpdate();

            //    for(let i = 0 ; i  <= countObjectJson ;  i++)   {
            //        console.log(this.$refs.gridLayout[i].layouts);
            //          console.log(dataJson[i]);
            //      this.$refs.gridLayout[i].layouts = dataJson[i];
            //      this.$refs.gridLayout[i].onWindowResize();
            //    }

            //    this.layout = response.data ;
            // this.layouts = response.data;
            //  this.$refs.gridLayout[0].layouts = response.data[0];
            //  this.$refs.gridLayout[0].onWindowResize();
            //  this.$refs.gridLayout[1].layouts = response.data[1];
            //  this.$refs.gridLayout[1].onWindowResize();
            //  setTimeout(this.copyValue,50);
          }
        })
        .catch((e) => {
          //  this.errors.push(e)
        });
      window.addEventListener("resize", this.handleResize);
      this.handleResize();
    },
    AddComponent: function (arg, grid) {
      //console.log("arg",arg);
      //   console.log("layout", arg["lg"]);

      let gridValue = grid;
      // if (this.masterLayout[gridValue] == undefined){
      //     this.masterLayout[gridValue] = [];
      // }
      if (this.layoutsAll[gridValue] == undefined) {
        this.layoutsAll[gridValue] = [];
      }

      //  console.log('this.layoutsAll[grid]', this.layoutsAll[grid]);

      //this.layouts[grid].push(arg) ;

      let idComponent = Number(this.componentIdNew);

      let allArgument = arg;
      //this.masterLayout[gridValue].push(allArgument);

      console.log("=== AddComponent START ===");
      console.log("grid:");
      console.log(grid);
      console.log("gridValue:");
      console.log(gridValue);
      console.log("allArgument:");
      console.log(allArgument);
      console.log("idComponent:");
      console.log(idComponent);

      console.log("this.masterLayout:");
      console.log(this.masterLayout);
      console.log("this.masterLayout[gridValue]:");
      console.log(this.masterLayout[gridValue]);

      console.log("this.layoutsAll:");
      console.log(this.layoutsAll);
      console.log("this.layouts:");
      console.log(this.layouts);

      let argument = arg["lg"][0];

      this.layoutsAll[gridValue].push(argument);

      this.componentIdNew = arg["lg"][0].i + 1;

      console.log("this.layoutsAll po pushu:");
      console.log(this.layoutsAll);
      console.log("this.componentIdNew:");
      console.log(this.componentIdNew);

      console.log("=== AddComponent END ===");

      // this.layoutsAll[3] = arg["lg"] ;

      //    this.layouts1["lg"] = this.layout1 ;
      //    this.layouts3["lg"] = this.layout1 ;
      //    this.layouts3["lg"] = this.layout1 ;

      //     this.layoutsAll

      // this.layouts[0] = arg ;
      //  console.log("layouts",this.layouts);
      //  this.layout.push(arg["lg"][0]) ;
      //  this.layouts[0]['lg'] = this.layout[0] ;

      // console.log("grid "+grid);
      // const  resolution = ["lg","md","sm","xs","xxs"];
      // if (this.layouts[grid] == undefined) {
      //     this.layouts[grid] = [] ;
      // }
      // if(this.layout[grid].length == 0){
      //     this.componentId[grid] = 0 ;

      // } else {
      //     console.log("Max",Math.max(...this.layout[grid].map(({ i }) => i)));
      //     this.componentId[grid] = Math.max(...this.layout[grid].map(({ i }) => i)) ;
      // }

      // this.componentId[grid] = this.componentId[grid] + 1  ;
      // console.log("component: ",this.componentId[grid]);

      // var row = {"x":4,"y":0,"w":4,"h":4,"i":this.componentId[grid],"moved":false,"editable":true} ;
      // var newRow = [];
      // for (var d = 0 ; d < 5; d++){
      //     // console.log("grid");
      //     // console.log(grid);
      //     if (this.layouts[grid][resolution[d]] == undefined)
      //     {

      //         this.layouts[grid][resolution[d]] = [] ;
      //     }
      //     // console.log("ddd "+this.layouts[grid][resolution[d]]);

      //     this.layouts[grid][resolution[d]].push(Object.assign({},row, arg[resolution[d]][0])) ;
      // }

      // console.log('grid:',this.layouts[grid]) ;
      // var gridElement = grid - 1;
      // this.$refs.gridLayout[gridElement].layouts = this.layouts[grid] ;
      // this.$refs.gridLayout[gridElement].onWindowResize();

      // setTimeout(this.copyValue(grid,gridElement),50);
      // this.check();
    },
    forceRerender: function () {
      this.componentKey += 1;
    },
    sendDataToDatabase: function () {
      // console.log(this.$refs.gridLayout[0].layouts);
      //console.log("layouts", this.saveDataLayersToBase);

      axios
        .post(
          "http://quizkreator.local/cms/data",
          this.saveDataLayersToDatabase
        )
        .then((reposne) => {
          console.log("data send");
        })
        .catch((error) => {});
    },

    handleResize: function () {
      this.window.width = document.body.clientWidth;
      this.window.height = window.innerHeight;
    },
    deleteIteam: function (arg) {
      let layoutsAll = this.layoutsAll;

      let id = arg;
      for (let b = 0; b < 3; b++) {
        var key = layoutsAll[b].filter(function (obj) {
          return obj.i == arg;
        });

        var object = key[0];
        // if (typeof object.background !== 'undefined') {
        //     this.deleteRemoteFile(object.background);
        // }
        var resolution = ["lg", "md", "sm", "xs", "xxs"];
        var cast = [];
        // for (let c = 0; c < 5; c++) {
        //     cast[b] = [] ;
        //     console.log(layoutsAll[b][resolution[c]]);
        //     cast[b][resolution[c]] = layoutsAll[b][resolution[c]].filter(function (obj) {
        //         return obj.i != id;
        //     });

        //     layoutsAll[b][resolution[c]] = cast[b][resolution[c]];
        // }
        let data = [];
        data[b] = layoutsAll[b].filter(function (obj) {
          return obj.i != id;
        });

        layoutsAll[b] = data[b];
        this.$set(this.layoutsAll, b, layoutsAll[b]);
      }

      //  console.log('this.layoutsAll', this.layoutsAll);
      this.layoutsAll = layoutsAll;
    },
    deleteRemoteFile(url) {
      axios
        .post(process.env.MIX_ROOT_API + "/deletefile/", {
          file: url,
        })
        .then((reposne) => {})
        .catch((error) => {});
    },
    layoutUpdatedEvent: function (newLayout) {
      console.log("Updated layout: ", newLayout);
    },
    moveEvent: function (i, newX, newY) {},
    resizeEvent: function (i, newH, newW) {
      // this.window.width = document.body.clientWidth;
      // console.log("name:"+event.target);
    },
    movedEvent: function (i, newX, newY) {
      //     //console.log(event.target.refence);
      //     console.log(event);
      //     console.log(event.target);
      //     console.log("move Iteam: " +  $(event.target).attr("refenceIteam"));
      //    let pathMoveIteamHtml = $(event.target).find('div').attr('refenceIteam');
      //     console.log("move Iteam: " + pathMoveIteamHtml);
      //    // console.log("move Iteam: " +  $(event.target)[1].attr("refenceIteam"));
      //    // console.log( $('<div class="big1" style="color: rgb(0, 0, 0); background-color: rgb(255, 255, 255); overflow: hidden;"><div refenceiteam="1"> Text domyślnydfsdf</div></div>').find('div').attr('refenceiteam'));
      //     // console.log("move console Iteam: " +  $(event.path[7]));
      //     console.log("move Grid: "+ $(event.path[8].innerHTML).attr("refenceGrid"));
      //     console.log("move Grid: "+ $(event.path[7].innerHTML).attr("refenceGrid"));
      //        let referenceToGrid = $(event.path[8].innerHTML).attr("refenceGrid");
      //     if(referenceToGrid === undefined) {
      //         referenceToGrid = $(event.path[7].innerHTML).attr("refenceGrid");
      //     }
      //     this.layouts[referenceToGrid][this.reposiveCss] = this.$refs.gridLayout[0].layouts[this.reposiveCss] ;
      //     this.check();
    },
    resizedEvent: function (i, newH, newW) {
      // console.log(event);
      // console.log(event.target);
      // console.log("resize Iteam: "+$(event.path[1].innerHTML).attr("refenceIteam"));
      // console.log("resize Grid: "+$(event.path[6].innerHTML).attr("refenceGrid"));
      // let referenceToGrid = $(event.path[6].innerHTML).attr("refenceGrid")
      // this.layouts[referenceToGrid][this.reposiveCss] = this.$refs.gridLayout[0].layouts[this.reposiveCss] ;
      // this.check();
    },
  },
};
</script>

<style>
.big1 {
  font-family: "Helvetica Neue", sans-serif;
  font-size: 75px;
  font-weight: bold;
  letter-spacing: -1px;
  line-height: 1;
  text-align: center;
  width: 100%;
  height: 100%;
}

.big2 {
  font-family: "Helvetica Neue", sans-serif;
  font-size: 45px;
  font-weight: bold;
  letter-spacing: -1px;
  line-height: 1;
  text-align: center;
  width: 100%;
  height: 100%;
}

.big3 {
  font-family: "Helvetica Neue", sans-serif;
  font-size: 30px;
  font-weight: bold;
  letter-spacing: -1px;
  line-height: 1;
  text-align: center;
  width: 100%;
  height: 100%;
}

.form-modal {
  padding: 20px;
  margin: 20px;
}

/*** EXAMPLE ***/
.resize {
  width: 100%;
}

.lg {
  width: 1280px;
}

.md {
  width: 1100px;
}

.sm {
  width: 900px;
}

.xs {
  width: 650px;
}

.xxs {
  width: 480px;
}

#content {
  margin: 0 auto;
}
.content {
  /* margin: 0 auto ; */
}
.minature {
  zoom: 30%;
  width: 600px;
  min-height: 300px;
  background-color: #EEEEEE;
  border: 1px solid black;
}

.resize-icon {
  font-size: 50px;
  padding: 20px;
}

.center {
  width: 100%;
  text-align: center;
  display: inline-flex;
  justify-content: center;
}

.icon-holder {
  padding: 20px;
  cursor: pointer;
}

.red {
  color: red;
}

.pencil {
  z-index: 100;
  bottom: -5px;
  right: 25px;
  width: 20px;
  height: 20px;
  margin: 10px;
  position: absolute;
  font-size: 20px;
  cursor: pointer;
  color: white;
  text-shadow: -1px 0 black, 0 1px black, 1px 0 black, 0 -1px black;
}

.trash {
  z-index: 100;
  bottom: -5px;
  right: 0px;
  width: 20px;
  height: 20px;
  margin: 10px;
  position: absolute;
  font-size: 20px;
  cursor: pointer;
  color: white;
  text-shadow: -1px 0 black, 0 1px black, 1px 0 black, 0 -1px black;
}

.vue-grid-layout {
  width: 100%;
  background: #eee;
}

.layoutJSON {
  background: #ddd;
  border: 1px solid black;
  margin-top: 10px;
  padding: 10px;
}

.eventsJSON {
  background: #ddd;
  border: 1px solid black;
  margin-top: 10px;
  padding: 10px;
  height: 100px;
  overflow-y: scroll;
}

.columns {
  -moz-columns: 120px;
  -webkit-columns: 120px;
  columns: 120px;
}

.vue-resizable-handle {
  z-index: 5000;
  position: absolute;
  width: 20px;
  height: 20px;
  bottom: 0;
  right: 0;
  background: url("data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBzdGFuZGFsb25lPSJubyI/Pg08IS0tIEdlbmVyYXRvcjogQWRvYmUgRmlyZXdvcmtzIENTNiwgRXhwb3J0IFNWRyBFeHRlbnNpb24gYnkgQWFyb24gQmVhbGwgKGh0dHA6Ly9maXJld29ya3MuYWJlYWxsLmNvbSkgLiBWZXJzaW9uOiAwLjYuMSAgLS0+DTwhRE9DVFlQRSBzdmcgUFVCTElDICItLy9XM0MvL0RURCBTVkcgMS4xLy9FTiIgImh0dHA6Ly93d3cudzMub3JnL0dyYXBoaWNzL1NWRy8xLjEvRFREL3N2ZzExLmR0ZCI+DTxzdmcgaWQ9IlVudGl0bGVkLVBhZ2UlMjAxIiB2aWV3Qm94PSIwIDAgNiA2IiBzdHlsZT0iYmFja2dyb3VuZC1jb2xvcjojZmZmZmZmMDAiIHZlcnNpb249IjEuMSINCXhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyIgeG1sbnM6eGxpbms9Imh0dHA6Ly93d3cudzMub3JnLzE5OTkveGxpbmsiIHhtbDpzcGFjZT0icHJlc2VydmUiDQl4PSIwcHgiIHk9IjBweCIgd2lkdGg9IjZweCIgaGVpZ2h0PSI2cHgiDT4NCTxnIG9wYWNpdHk9IjAuMzAyIj4NCQk8cGF0aCBkPSJNIDYgNiBMIDAgNiBMIDAgNC4yIEwgNCA0LjIgTCA0LjIgNC4yIEwgNC4yIDAgTCA2IDAgTCA2IDYgTCA2IDYgWiIgZmlsbD0iIzAwMDAwMCIvPg0JPC9nPg08L3N2Zz4=");
  background-position: bottom right;
  padding: 0 3px 3px 0;
  background-repeat: no-repeat;
  background-origin: content-box;
  box-sizing: border-box;
  cursor: se-resize;
}

.vue-grid-item:not(.vue-grid-placeholder) {
  background: #fff;
  border: 1px solid black;
}

.vue-grid-item.resizing {
  opacity: 0.9;
}

.vue-grid-item.static {
  background: #cce;
}

.vue-grid-item .text {
  font-size: 24px;
  text-align: center;
  position: absolute;
  top: 0;
  bottom: 0;
  left: 0;
  right: 0;
  margin: auto;
  height: 24px;
}

.vue-grid-item .minMax {
  font-size: 12px;
}

.vue-grid-item .add {
  cursor: pointer;
}

.vue-grid-item {
  touch-action: none;
}

.card {
  /* Add shadows to create the "card" effect */
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
  transition: 0.3s;
  padding: 50px;
}

/* On mouse-over, add a deeper shadow */
.card:hover {
  box-shadow: 0 8px 16px 0 rgba(0, 0, 0, 0.2);
}

/* Add some padding inside the card container */
.container {
  padding: 2px 16px;
}
</style>
