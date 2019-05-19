<template>
<b-container fluid>
    <b-row class="m-1">
        <b-col class="p-0">
            <b-form-file
                @change="loadFileCSV"
                v-model="filecsv"
                placeholder="CSV file..."
                drop-placeholder="Drop file here..."
            ></b-form-file>            
        </b-col>
        <b-col class="p-0" cols="1">
            <b-button class="float-left" @click="filecsv=null">reset</b-button>
        </b-col>
    </b-row>
    <b-row class="m-1">
        <b-form-textarea
        :value="csvtext"
        @input="csvchange"
        id="textarea-auto-height"
        placeholder="CSV Content..."
        rows="4"
        max-rows="8"
        ></b-form-textarea>
    </b-row>
    <b-row class="m-1">
        <b-col class="p-0">
            <b-form-file
            @change="loadFileTemplate"
            v-model="filetmp"
            placeholder="Template file..."
            drop-placeholder="Drop file here..."
            ></b-form-file>
        </b-col>
        <b-col class="p-0" cols="1">
            <b-button class="float-left" @click="filetmp=null">reset</b-button>
        </b-col>

    </b-row>
    <b-row class="m-1">
        <b-form-textarea
        :value="template"
        @input="tmpchange"
        id="textarea-auto-height"
        placeholder="Template file with variables enclosed in <>"
        rows="4"
        max-rows="8"
        ></b-form-textarea>
    </b-row>  
    <b-row class="m-1">
        <result-box
            :message="results[currentPage-1].result"
        ></result-box>    
        <div class="overflow-auto"> 
            <!-- Use text in props -->
            <b-pagination
            class="mb-0"
            v-model="currentPage"
            :total-rows="rows"
            :per-page="perPage"
            first-text="First"
            prev-text="Prev"
            next-text="Next"
            last-text="Last"
            ></b-pagination>
        </div> 
        <b-dropdown variant="primary" right text="Save">
            <b-dropdown-item @click="saveFile('template.csv', csvtext)">Save CSV table</b-dropdown-item>
            <b-dropdown-item @click="saveFile('template.txt', template)">Save template</b-dropdown-item>
            <b-dropdown-item @click="saveZipFiles('results.zip', results)">Save results in ZIP</b-dropdown-item>
            <b-dropdown-item @click="saveMrgFiles('merged.txt', results)">Save results merged</b-dropdown-item>
            <b-dropdown-divider v-if="false"></b-dropdown-divider>
            <b-dropdown-item v-if="false">Save all in ZIP</b-dropdown-item>
        </b-dropdown>  
    </b-row>
</b-container>
</template>

<script>
const csvj=require('csvtojson/v2')
const JSZip=require('jszip')
import { saveAs } from 'file-saver';
import ResultBox from './ResultBox.vue'

export default {
  name: 'Generator',
  components: {
    ResultBox
  },
  props: {
    id: {
      default: "GeneratorId",
      type: String
    }
  },
  data: function(){
    return{
      username: 'myuser',
      password: 'mypass',
      csvtext: "name,index,ip,mask\nR1,1/1,1.1.1.1,255.255.255.0\nR2,1/2,1.1.2.1,255.255.255.0\nR3,1/3,1.1.3.1,255.255.255.0",
      template: "interface Ethernet<index>\n desciption <name>\n ip address <ip> <mask> \n",
      filecsv: "",
      filetmp: "",
      rows: 1,
      currentPage: 1,
      perPage: 1,
      results: [{results: ""}]
    }
  },
  methods:{
    csvchange: function(v){
        this.csvtext = v
        this.computeResults()
    },
    tmpchange: function(t){
        this.template = t
        this.computeResults()
    },
    computeResults(){
        return csvj().fromString(this.csvtext)
        .then((array)=>{
            let newresult = []
            if (array.length){
                this.rows = array.length
            }else{
                this.results = [{results: ""}]
                this.rows= 1
                this.currentPage= 1
                this.perPage= 1
                return false;
            }
            for( let i = 0 ; i< array.length; i++){
                let json = array[i]
                let template = this.template
                let result = template
                let name = "row"+(i+1).toString()
                for( let col in json){
                    let reg = new RegExp("<"+col+">")
                    result = result.replace(reg,json[col])
                    if (col == "name"){
                        name = json[col]
                    }
                }
                newresult.push({result: result, name:name})
            }
            this.results = newresult
        })   
    },
    saveFile: function(filename,text) {
        const blob = new Blob([text], {type: 'text/plain'})
        const e = document.createEvent('MouseEvents'),
        a = document.createElement('a');
        a.download = filename;
        a.href = window.URL.createObjectURL(blob);
        a.dataset.downloadurl = ['text/json', a.download, a.href].join(':');
        e.initEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null);
        a.dispatchEvent(e);
    },
    saveZipFile: function(filename,text) {
        let zip = new JSZip();
        zip.file("Hello.txt", text)
        zip.generateAsync({type:"blob"})
        .then(function (blob) {
            saveAs(blob, filename);
        });
    },
    saveZipFiles: function(filename,array) {
        let zip = new JSZip();
        for(let i = 0; i<array.length; i++){
            let file = array[i]
            // zip.file("file"+i.toString()+".txt",file.result)
            zip.file(file.name,file.result)
        }
        zip.generateAsync({type:"blob"})
        .then(function (blob) {
            saveAs(blob, filename);
        });
    },
    saveMrgFiles: function(filename,array) {
        let mergedText = ""
        for(let i = 0; i<array.length; i++){
            let file = array[i]
            mergedText +=file.result
        }
        this.saveFile(filename,mergedText)
    },
    loadFileTemplate: function(event) {
        var input = event.target;
        var reader = new FileReader();
        let _this = this
        reader.onload = function(){
            _this.template = reader.result
            _this.computeResults()
        };
        reader.readAsBinaryString(input.files[0]);
    },
    loadFileCSV: function(event) {
        var input = event.target;
        var reader = new FileReader();
        let _this = this
        reader.onload = function(){
            _this.csvtext = reader.result
            _this.computeResults()
        };
        reader.readAsBinaryString(input.files[0]);

    }
  },
  mounted: function () {
    this.$nextTick(() => {
      this.computeResults()
    })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
