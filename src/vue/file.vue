<template>
    <div :class="question.cssClasses.root">
        <input v-if="!question.isReadOnly" type="file" :id="question.inputId" @change="doChange" v-bind:aria-label="question.locTitle.renderedHtml" :multiple="question.allowMultiple ? 'multiple' : undefined"/>
        <button v-if="hasValue" :class="question.cssClasses.removeButton" @click="doClean">{{question.cleanButtonCaption}}</button>
        <input v-if="question.isReadOnly" type="text" readonly :class="getPlaceholderClass()" :placeholder="question.title" />
        <div v-if="hasValue">
            <img v-for="(val, index) in question.previewValue" :key="question.inputId + '_' + index" v-show="val" :class="question.cssClasses.preview" :src="val" :height="question.imageHeight" :width="question.imageWidth" alt="File preview"/>
        </div>
    </div>
</template>

<script lang="ts">
import Vue from "vue";
import { Component, Prop } from "vue-property-decorator";
import { default as Question } from "./question";
import { QuestionFileModel } from "../question_file";

@Component
export class File extends Question<QuestionFileModel> {
  hasValue = false;
  doChange(e) {
    var src = e.target || e.srcElement;
    if (!window["FileReader"]) return;
    if (!src || !src.files || src.files.length < 1) return;
    let files = [];
    for (let i = 0; i < src.files.length; i++) {
      files.push(src.files[i]);
    }
    this.question.loadFiles(files);
    this.hasValue = true;
  }
  doClean(event) {
    var src = event.target || event.srcElement;
    this.question.clear();
    src.parentElement.querySelectorAll("input")[0].value = "";
    this.hasValue = false;
  }
  getPlaceholderClass() {
    return "form-control " + this.question.cssClasses.placeholderInput;
  }
}
Vue.component("survey-file", File);
    export default File;
</script>
