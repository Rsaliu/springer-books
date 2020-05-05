<template>
    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-10">
                  <div>
                      <b-container fluid>
    <!-- User Interface controls -->
                        <b-row>
        <b-col lg="6" class="my-1">
        <b-form-group
          label="Filter"
          label-cols-sm="3"
          label-align-sm="right"
          label-size="sm"
          label-for="filterInput"
          class="mb-0"
        >
          <b-input-group size="sm">
            <b-form-input
              v-model="filter"
              type="search"
              id="filterInput"
              placeholder="Type to Search"
            ></b-form-input>
            <b-input-group-append>
              <b-button :disabled="!filter" @click="filter = ''">Clear</b-button>
            </b-input-group-append>
          </b-input-group>
        </b-form-group>
      </b-col>
                  <b-table style="font-size:12px" small responsive striped hover :items="tablesData" :fields="fields" :bordered="true" @filtered="onFiltered"  :filter="filter"
      :filterIncludedFields="filterOn" :current-page="currentPage" :per-page="perPage" >
                          <template v-slot:cell(download)="row">
                            <a v-bind:href="row.item.OpenURL"
  v-bind:download="row.item['Book Title']"
  target="_blank"
>
  Download
</a>
                          <b-button style="font-size:12px" size="sm" @click="downloadSingle(row.item, row.index, $event.target)" class="mr-1">
                            Download
                          </b-button>

                        </template>

                  </b-table>
                        <b-col sm="7" md="6" class="my-1">
        <b-pagination
          v-model="currentPage"
          :total-rows="totalRows"
          :per-page="perPage"
          align="fill"
          size="sm"
          class="my-0"
        ></b-pagination>
      </b-col>
        <b-col sm="7" md="6" class="my-1">
            <b-button size="sm" @click="info(row.item, row.index, $event.target)" class="mr-1">Download All</b-button>
            <b-button size="sm" @click="info(row.item, row.index, $event.target)" class="mr-1">Download Selection</b-button>
      </b-col>
                        </b-row>
                      </b-container>
                </div>

            </div>
        </div>
    </div>
</template>

<script>
import json from '../data.json'
    export default {
        data(){
          return{
            totalRows: 1,
            currentPage: 1,
            jsonData:JSON.parse(json),
            filteredData:[],
            tablesData:[],
            fields:[
          { key: 'S.No.', sortable: true },
          { key: 'Book Title', sortable: true },
          { key: 'Author', sortable: true },
          { key: 'Edition', sortable: false },
          { key: 'OpenURL', sortable: false },
          { key: 'download', label: 'Download' }
            ],
            filter: null,
            filterOn: [],
            perPage: 10,
          }
        },
        mounted() {
            console.log('Component mounted.')
            this.filteredData=this.jsonData
            console.log(this.jsonData[0]);
            this.tablesData = Object.keys(this.jsonData).map((key) => {
            return this.jsonData[key]})
            this.totalRows = this.tablesData.length

        },
        methods:{
        onFiltered(filteredItems) {
        // Trigger pagination to update the number of buttons/pages due to filtering
        this.totalRows = filteredItems.length
        this.currentPage = 1
      },
          forceFileDownload(response){
      const url = window.URL.createObjectURL(new Blob([response.data]))
      const link = document.createElement('a')
      link.href = url
      link.setAttribute('download', 'file.png') //or any other extension
      document.body.appendChild(link)
      link.click()
    },
      downloadSingle(item, index, button){
        if(confirm("Do you really want to download")){
          console.log(item.OpenURL)
          //axios.get(item.OpenURL).catch((error)=>alert(error))
        this.$http({
        method: 'get',
        url: item.OpenURL,
        responseType: 'arraybuffer'
      })
      .then(response => {
        this.forceFileDownload(response)  
      })
      .catch(() => console.log('error occured'))
        }
      }
        }
        
    }
</script>
