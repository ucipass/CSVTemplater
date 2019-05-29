<template>
<b-container fluid>
    <b-container fluid class="disp-flex-vertical">
        <b-row v-if='visible_csv' class="flex-fix"><label class='mt-2'>CSV Table</label></b-row>
        <b-row v-if='visible_csv' :class="csvclass">
            <b-form-textarea
            :class="csvclass"
            :value="csvtext"
            @input="csvchange"
            id="textarea-auto-height"
            placeholder="Enter CSV formatted text here..."
            rows = 5
            ></b-form-textarea>
        </b-row >
        <b-row v-if='visible_csv' class="flex-fix">
            <b-button-group>
                <label class="btn btn-outline-primary m-0">Load
                    <input 
                    type="file"
                    @change="loadFileCSV"
                    :value="filecsv"
                    style="display: none;">
                </label>    
                <b-button variant="outline-primary" class="float-left" @click="csvtext=''">Clear</b-button>
            </b-button-group>
        </b-row>
        <b-row v-if='visible_tmp || visible_results' class="flex-auto">
            <b-col  v-if='visible_tmp' class='p-0'>
                <div class="disp-flex-vertical">
                    <div align='left' class="flex-fix">
                        <label class='mt-2'>Template</label>
                    </div>
                        <b-form-textarea
                            class="flex-auto"
                            :value="template"
                            @input="tmpchange"
                            id="textarea-auto-height"
                            placeholder="Template file with variables enclosed in <>"
                            rows="5">
                        </b-form-textarea>
                    <div align='left' class="flex-fix">
                        <b-button-group>
                            <label class="btn btn-outline-primary m-0">Load
                                <input 
                                type="file"
                                @change="loadFileTemplate"
                                :value="filetmp"
                                style="display: none;">
                            </label>    
                            <b-button variant="outline-primary" class="float-left" @click="template=''">Reset</b-button>
                        </b-button-group>
                    </div>
                </div>
            </b-col>
            <b-col  v-if='visible_results' class='p-0'>
                <div class="disp-flex-vertical">
                    <div align='left' class="flex-fix">
                        <label class='mt-2'>Results</label>
                    </div>
                    <!-- <b-form-textarea class="flex-auto"></b-form-textarea> -->
                    <result-box class='flex-auto' :message="currentResult"></result-box>
                    <div align='left' class="flex-fix">
                        <b-row align-h="between">
                            <b-dropdown class='ml-3' variant="outline-primary" right text="Save">
                                <b-dropdown-item @click="saveFile('template.csv', csvtext)">Save CSV table</b-dropdown-item>
                                <b-dropdown-item @click="saveFile('template.txt', template)">Save template</b-dropdown-item>
                                <b-dropdown-item @click="saveZipFiles('results.zip', results)">Save results in ZIP</b-dropdown-item>
                                <b-dropdown-item @click="saveMrgFiles('merged.txt', results)">Save results merged</b-dropdown-item>
                                <b-dropdown-divider v-if="false"></b-dropdown-divider>
                                <b-dropdown-item v-if="false">Save all in ZIP</b-dropdown-item>
                            </b-dropdown>                                       
                            <div class="overflow-auto mr-3"> 
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
                        </b-row>
                    </div>
                </div>
            </b-col>
            
        </b-row>
    </b-container>
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
      results: [{result: ""}],
      visible_csv: true,
      visible_tmp: true,
      visible_results: true
    }
  },
    computed: {
        currentResult: function(){
            let r = this.results[this.currentPage-1].result
            return r
        },
        csvclass: function(){
            if ( this.visible_results || this.visible_tmp) {
                return "flex-fix"
            } else{
                return "flex-auto"
            }
        }
  },
  methods:{
    test: function(){
        console.log('Test function run!')
    },
    csvchange: function(v){
        let _this = this
        this.csvtext = v
        this.computeResults()
        .then((success)=>{
            if (! success){
                // console.log("CSV Change failed")
                _this.results = [{result: _this.template}]
            }
            else{
                // console.log("CSV Change success")
            }

        })
    },
    tmpchange: function(t){
        let _this = this
        this.template = t
        this.computeResults()
        .then((success)=>{
            if (! success){
                // console.log("CSV Change failed")
                _this.results = [{result: _this.template}]
            }
            else{
                // console.log("CSV Change success")
            }

        })
    },
    computeResults(){
        return csvj().fromString(this.csvtext)
        .then((array)=>{
            let newresult = []
            if (array.length){
                this.rows = array.length
            }else{
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
            return true
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
    this.$root.$on('checkedtmpEvent', (event) => {
        // console.log("Generator", event)
        this.visible_tmp = !event;
    })
    this.$root.$on('checkedcsvEvent', (event) => {
        // console.log("Generator", event)
        this.visible_csv = !event;
    })
    this.$root.$on('checkedresultsEvent', (event) => {
        // console.log("Generator", event)
        this.visible_results = !event;
    })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.disp-flex-vertical {
  display: flex;
  flex-flow: column;
  height: 100%;
}
.disp-flex-horizontal {
  display: flex;
  flex-flow: row;
  width: 100%;
}

.flex-fix {
  /* border: 1px dotted grey; */
  flex-grow: 0;
  flex-shrink: 0;
  flex-basis: auto;
}

.flex-auto {
  /* border: 1px dotted grey; */
  flex-grow: 1;
  flex-shrink: 1;
  flex-basis: auto;
}
</style>
