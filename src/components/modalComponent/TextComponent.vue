<template>
  <div>
    <div class="row form-modal">
      <div class="col-sm-6">
        <label for="exampleFormControlSelect1">Rozdzielczość</label>
        <select
          @change="updateBackgroundChange"
          v-model="cssResolution"
          class="form-control"
          id="exampleFormControlSelect0"
        >
          <option value="all" selected>Wszystkie</option>
          <option value="lg">lg</option>
          <option value="md">md</option>
          <option value="sm">sm</option>
          <option value="xs">xs</option>
          <option value="xxs">xxs</option>
        </select>

        <div class="form-group">
          <label for="exampleFormControlSelect1">Czcionka</label>
          <select
            @change="updateFontSize"
            v-model="font[cssResolution]"
            class="form-control"
            id="exampleFormControlSelect1"
          >
            <option class="big1" selected>big1</option>
            <option class="big2">big2</option>
            <option class="big3">big3</option>
          </select>
        </div>

        Kolor czcionki:<br />
        <compact-picker
          refs="colorPicker"
          @input="updateColor"
          v-model="colors[cssResolution]"
        /><br />
        <slider-picker
          @input="updateColor"
          v-model="colors[cssResolution]"
        /><br />
        Kolor tła:<br />
        <compact-picker
          refs="bgColorPicker"
          @input="updateBackground"
          v-model="bgcolor[cssResolution]"
        /><br />
        <slider-picker
          @input="updateBackground"
          v-model="bgcolor[cssResolution]"
        /><br />
        <div class="form-group">
          <label for="exampleFormControlSelect2">Tło</label>
          <select
            @change="updateBackgroundRepeat"
            v-model="imageBackgroundRepeat[cssResolution]"
            class="form-control"
            id="exampleFormControlSelect2"
          >
            <option value="no-repeat" selected>Pojedyczo</option>
            <option value="repeat">Powtarzaj</option>
          </select>
          <select
            @change="updateBackgroundSize"
            v-model="imageBackgroundSize[cssResolution]"
            class="form-control"
            id="exampleFormControlSelect3"
          >
            <option value="auto" selected>Normalnie</option>
            <option value="100% 100%">Rozciągnij</option>
          </select>
        </div>
        <vue-dropzone
          ref="myVueDropzone"
          id="dropzone"
          :options="dropzoneOptions"
          :useCustomSlot="true"
          v-on:vdropzone-success="addAnalysisId"
        >
          <div class="dropzone-custom-content">
            <h3 class="dropzone-custom-title">Wgraj obrazki</h3>
            <div class="subtitle">
              Przeciągnij grafiki , albo kliknij aby otworzyć okno dialogowe
            </div>
          </div>
        </vue-dropzone>
      </div>
      <div class="col-sm-6">
        <div class="form-group text-center">
          <label for="exampleFormControlTextarea1">Text</label>
          <textarea
            @change="updateText"
            v-model="modalPreviewText[cssResolution]"
            class="form-control"
            id="exampleFormControlTextarea1"
            rows="3"
          ></textarea>
          <br />

          <button
            type="button"
            v-if="!editComponentText"
            @click="insertTextComponent"
            class="btn btn-primary"
          >
            Wstaw
          </button>
          <button
            type="button"
            v-if="editComponentText"
            @click="editTextComponent"
            class="btn btn-primary"
          >
            Zapisz
          </button>
          <!-- <button type="button"  @click="test" class="btn btn-primary" >test</button> -->
        </div>
        <br />
        Podgląd
        <div
          refs="preview"
          :class="font[cssResolution]"
          v-bind:style="stylePreview[cssResolution]"
        >
          {{ modalPreviewText[cssResolution] }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Slider, Compact } from "vue-color";
import vue2Dropzone from "vue2-dropzone";
import "vue2-dropzone/dist/vue2Dropzone.min.css";

export default {
  name: "TextComponent",
  components: {
    "slider-picker": Slider,
    "compact-picker": Compact,
    vueDropzone: vue2Dropzone,
  },
  data() {
    return {
      i: {},
      x: {},
      y: {},
      w: {},
      h: {},
      editable: {},
      imageBackgroundRepeat: {},
      imageBackgroundSize: {},
      completeObject: [],
      stylePreview: {
        all: { color: "#000" },
        lg: { color: "#000" },
        md: { color: "#000" },
        sm: { color: "#000" },
        xs: { color: "#000" },
        xxs: { color: "#000" },
      },
      modalPreviewText: {},
      colors: {
        all: { hex: "#000" },
        lg: { hex: "#000" },
        md: { hex: "#000" },
        sm: { hex: "#000" },
        xs: { hex: "#000" },
        xxs: { hex: "#000" },
      },
      bgcolor: {
        all: { hex: "#fff" },
        lg: { hex: "#fff" },
        md: { hex: "#fff" },
        sm: { hex: "#fff" },
        xs: { hex: "#fff" },
        xxs: { hex: "#fff" },
      },
      font: {},
      editedId: 0,
      newId: 0,
      editComponentText: false,
      cssResolution: "all",
      imageBackground: {},
      dropzoneOptions: {
        maxFiles: 1,
        autoProcessQueue: true,
        addRemoveLinks: true,
        queuecomplete: function (file, response) {},
        url: process.env.MIX_ROOT_API + "/api/backgroud",
        createImageThumbnails: true,
        maxFilesize: 10,
        headers: { "My-Awesome-Header": "header value" },
        dictRemoveFile: "Usuń plik",
      },
    };
  },
  created: function () {},
  props: ["editValue", "editView", "editGrid", "componentIdNew"],
  watch: {
    editView: {
      immediate: true,
      handler(val, oldVal) {
        this.cssResolution = val;
      },
    },
    componentId: {
      immediate: true,
      handler(val, oldVal) {
        this.newId = val;
      },
    },
    editValue: {
      immediate: true,
      handler(val, oldVal) {
        var handler = val;
        handler["all"] = { ...handler["lg"] };
        // handler = val['lg'][0] ;
        if (typeof handler["lg"][0].i != "undefined") {
          this.editedId = handler["lg"][0].i;
          this.editComponentText = true;
        }

        var resolution = ["all", "lg", "md", "sm", "xs", "xxs"];
        for (var c = 0; c < 6; c++) {
          if (typeof handler["lg"][0].i != "undefined") {
            this.$set(this.x, resolution[c], handler[resolution[c]][0].x);
            this.$set(this.y, resolution[c], handler[resolution[c]][0].y);
            this.$set(this.w, resolution[c], handler[resolution[c]][0].w);
            this.$set(this.h, resolution[c], handler[resolution[c]][0].h);
            this.$set(
              this.editable,
              resolution[c],
              handler[resolution[c]][0].editable
            );
            // this.$refs.colorPicker.color = handler[resolution[c]][0].color ;
            // this.$refs.bgColorPicker.color = handler[resolution[c]][0].bgcolor ;
          }
          this.$set(
            this.modalPreviewText,
            resolution[c],
            handler[resolution[c]][0].body
          );
          this.$set(this.font, resolution[c], handler[resolution[c]][0].font);
          this.$set(
            this.colors,
            resolution[c].hex,
            handler[resolution[c]][0].color
          );
          this.colors[resolution[c]].hex = handler[resolution[c]][0].color;
          this.$set(
            this.bgcolor,
            resolution[c].hex,
            handler[resolution[c]][0].bgcolor
          );
          this.bgcolor[resolution[c]].hex = handler[resolution[c]][0].bgcolor;
          this.$set(
            this.imageBackground,
            resolution[c],
            handler[resolution[c]][0].background
          );
          this.$set(
            this.imageBackgroundRepeat,
            resolution[c],
            handler[resolution[c]][0].backgroundRepeat
          );
          this.$set(
            this.imageBackgroundSize,
            resolution[c],
            handler[resolution[c]][0].backgroundSize
          );
          this.$set(
            this.stylePreview,
            resolution[c].color,
            handler[resolution[c]][0].color
          );
          this.stylePreview[resolution[c]].color = this.colors[
            resolution[c]
          ].hex;
          this.$set(
            this.stylePreview,
            resolution[c].backgroundColor,
            handler[resolution[c]][0].bgcolor
          );
          this.stylePreview[resolution[c]].backgroundColor = this.bgcolor[
            resolution[c]
          ].hex;
          this.updateBackroundImage();
        }
      },
    },
  },
  methods: {
    updateBackgroundChange: function (event) {
      // console.log(event.target.value);
      this.stylePreview[event.target.value].backgroundImage =
        "url(" +
        process.env.MIX_ROOT_API +
        "/images/" +
        this.imageBackground[event.target.value] +
        ")";
      this.stylePreview[
        event.target.value
      ].backgroundRepeat = this.imageBackgroundRepeat[event.target.value];
      this.stylePreview[
        event.target.value
      ].backgroundSize = this.imageBackgroundSize[event.target.value];
    },
    updateFontSize: function (event) {
      if (this.cssResolution == "all") {
        var resolution = ["all", "lg", "md", "sm", "xs", "xxs"];
        for (var c = 0; c < 6; c++) {
          this.font[resolution[c]] = event.target.value;
        }
      }
    },
    updateText: function () {
      if (this.cssResolution == "all") {
        var resolution = ["all", "lg", "md", "sm", "xs", "xxs"];
        for (var c = 0; c < 6; c++) {
          this.modalPreviewText[resolution[c]] = event.target.value;
        }
      }
    },
    updateBackgroundRepeat: function (event) {
      if (this.cssResolution == "all") {
        var resolution = ["all", "lg", "md", "sm", "xs", "xxs"];
        for (var c = 0; c < 6; c++) {
          this.stylePreview[resolution[c]].backgroundRepeat =
            event.target.value;
          this.imageBackgroundRepeat[resolution[c]] = event.target.value;
        }
      } else {
        this.stylePreview[this.cssResolution].backgroundRepeat =
          event.target.value;
      }
    },
    updateBackgroundSize: function (event) {
      if (this.cssResolution == "all") {
        var resolution = ["all", "lg", "md", "sm", "xs", "xxs"];
        for (var c = 0; c < 6; c++) {
          this.stylePreview[resolution[c]].backgroundSize = event.target.value;
          this.imageBackgroundSize[resolution[c]] = event.target.value;
        }
      } else {
        this.stylePreview[this.cssResolution].backgroundSize =
          event.target.value;
      }
    },
    updateColor: function (value) {
      if (this.cssResolution == "all") {
        var resolution = ["all", "lg", "md", "sm", "xs", "xxs"];
        for (var c = 0; c < 6; c++) {
          this.stylePreview[resolution[c]].color = value.hex;
          this.colors[resolution[c]].hex = value.hex;
        }
      } else {
        this.stylePreview[this.cssResolution].color = value.hex;
      }
    },
    updateBackground: function (value) {
      if (this.cssResolution == "all") {
        var resolution = ["all", "lg", "md", "sm", "xs", "xxs"];
        for (var c = 0; c < 6; c++) {
          this.stylePreview[resolution[c]].backgroundColor = value.hex;
          this.bgcolor[resolution[c]].hex = value.hex;
        }
      } else {
        this.stylePreview[this.cssResolution].backgroundColor = value.hex;
      }
    },
    updateBackroundImageForAll: function (data) {
      if (this.cssResolution == "all") {
        var resolution = ["all", "lg", "md", "sm", "xs", "xxs"];
        for (var c = 0; c < 6; c++) {
          this.imageBackground[resolution[c]] = data;
          this.stylePreview[resolution[c]].backgroundImage =
            "url(" + process.env.MIX_ROOT_API + "/images/" + data + ")";
        }
      }
    },
    insertEditValue: function (value) {},
    insertTextComponent: function () {
      var resolution = ["all", "lg", "md", "sm", "xs", "xxs"];
      var completeArrayOfPage = {};
      for (var c = 0; c < 6; c++) {
        if (resolution[c] != "all") {
          completeArrayOfPage[resolution[c]] = [];
          completeArrayOfPage[resolution[c]].push({
            font: this.font[resolution[c]],
            body: this.modalPreviewText[resolution[c]],
            color: this.colors[resolution[c]].hex,
            bgcolor: this.bgcolor[resolution[c]].hex,
            background: this.imageBackground[resolution[c]],
            backgroundSize: this.imageBackgroundSize[resolution[c]],
            backgroundRepeat: this.imageBackgroundRepeat[resolution[c]],
            type: "text",
            i: this.componentIdNew,
            x: 1,
            y: 1,
            w: 5,
            h: 2,
            editable: true,
          });
        }
      }
      this.$emit("insertComponent", completeArrayOfPage, this.editGrid);

      // this.$emit("insertComponent",{
      //     "font":this.font[this.cssResolution],
      //     "body":this.modalPreviewText[this.cssResolution],
      //     "color":this.colors[this.cssResolution].hex,
      //     'bgcolor':this.bgcolor[this.cssResolution].hex,
      //     'background':this.imageBackground[this.cssResolution],
      //     'backgroundSize': this.imageBackgroundSize[this.cssResolution],
      //     'backgroundRepeat': this.imageBackgroundRepeat[this.cssResolution]

      // });
    },
    editTextComponent: function () {
      var resolution = ["all", "lg", "md", "sm", "xs", "xxs"];
      var completeArrayOfPage = {};
      for (var c = 0; c < 6; c++) {
        if (resolution[c] != "all") {
          completeArrayOfPage[resolution[c]] = [];
          completeArrayOfPage[resolution[c]].push({
            i: this.editedId,
            x: this.x[resolution[c]],
            y: this.y[resolution[c]],
            w: this.w[resolution[c]],
            h: this.h[resolution[c]],
            editable: this.editable[resolution[c]],
            font: this.font[resolution[c]],
            body: this.modalPreviewText[resolution[c]],
            color: this.colors[resolution[c]].hex,
            bgcolor: this.bgcolor[resolution[c]].hex,
            background: this.imageBackground[resolution[c]],
            backgroundSize: this.imageBackgroundSize[resolution[c]],
            backgroundRepeat: this.imageBackgroundRepeat[resolution[c]],
            type: "text",
          });
        }
      }

      this.$emit("editComponent", completeArrayOfPage, this.editGrid);
    },
    updateBackroundImage: function (response) {
      this.stylePreview[this.cssResolution].backgroundImage =
        "url(" +
        process.env.MIX_ROOT_API +
        "/images/" +
        this.imageBackground[this.cssResolution] +
        ")";
      this.stylePreview[this.cssResolution].color = "#FDF";
      this.stylePreview[this.cssResolution].color = this.colors[
        this.cssResolution
      ].hex;
      this.stylePreview[this.cssResolution].backgroundColor = this.bgcolor[
        this.cssResolution
      ].hex;
      this.stylePreview[
        this.cssResolution
      ].backgroundRepeat = this.imageBackgroundRepeat[this.cssResolution];
      this.stylePreview[
        this.cssResolution
      ].backgroundSize = this.imageBackgroundSize[this.cssResolution];
    },
    addAnalysisId: function (file, response) {
      this.imageBackground[this.cssResolution] = response;

      this.updateBackroundImage();
      this.updateBackroundImageForAll(response);
    },
  },
};
</script>
<style scoped>
</style>
