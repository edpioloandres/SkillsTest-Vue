<template>
  <div class="about">
    <v-container fluid>
      <v-row align="center" justify="center" style="height:100vh"   >
        <v-card style="width: 650px; background-color: #f6f6f5;" class="rounded-lg fill-height pa-4" flat>
          <v-text-field v-model="search" clearable placeholder="Search..." outlined dense></v-text-field>

          <v-card @scroll="onScroll" flat height="85vh" style="overflow-y: auto; background-color: #f6f6f5;">
            <v-card-text>
              <v-lazy transition="fade-transition" :options="{ threshold: 0.8 }" v-for="(item , index) in searchData" :key="index">
                <v-card class="pa-3 mb-8" tile elevation="2" >
                  <v-toolbar flat tile >
                    <v-toolbar-title class="mr-3"><b>{{ item.mission_name }}</b></v-toolbar-title>

                    <v-chip v-if="item.launch_success == true" color="#67f33f" small label>
                      success
                    </v-chip>

                    <v-chip v-else-if="item.upcoming == true" color="#64f9f4" small label>
                      upcoming
                    </v-chip>

                    <v-chip v-else color="red" small label>
                      failed
                    </v-chip>
                  </v-toolbar>

                  <v-expansion-panels flat>
                    <v-expansion-panel>
                      <v-expansion-panel-content>
                        <v-toolbar flat dense>
                          {{ computeDate(item.launch_year) }} Years Ago |&nbsp;<a :href="item.links.article_link">Article</a>&nbsp;|&nbsp;<a :href="item.links.video_link">Video</a>
                        </v-toolbar>

                        <v-card flat>
                          <v-card-text>
                            <v-row>
                              <v-col cols="4">
                                <v-img v-if="item.links.mission_patch != null" :src="item.links.mission_patch"></v-img>
                                <span v-else>No image yet.</span>
                              </v-col>
                              <v-col cols="8">
                                <span v-if="item.details != null">{{ item.details }}</span>
                                <span v-else>No image yet.</span>
                              </v-col>
                            </v-row>
                          </v-card-text>
                        </v-card>
                      </v-expansion-panel-content>

                      <v-expansion-panel-header expand-icon="">    
                        <v-btn @click="item.expand = !item.expand" depressed color="primary">
                          <span v-if="item.expand == false">View</span>
                          <span v-else>Hide</span>
                        </v-btn>
                      </v-expansion-panel-header>
                    </v-expansion-panel>
                  </v-expansion-panels>

                </v-card>
              </v-lazy>

              <v-progress-circular
                v-show="showLoading"
                class="flex-center"
                indeterminate
                color="primary"
              ></v-progress-circular>

              <span class="flex-center" v-show="isEnd">End of list.</span>
              <span class="flex-center" v-show="searchData.length == 0 && this.search != ''">No record found.</span>
              
            </v-card-text>
          </v-card>
        </v-card>
      </v-row>
    </v-container>
  </div>
</template>

<script>
import axios from 'axios'
import moment from 'moment'
  export default {
    data : () => ({
      displayedData: [],
      length: 10,
      search: "",
      showLoading: false,
      isEnd: false,
      isExist: false,
    }),

    computed : {
      searchData(){
        if(this.search){
          return this.displayedData.filter(rec=>{
            return rec.mission_name.toUpperCase().includes(this.search.toUpperCase())
          })
        }else{
          return this.displayedData.slice(0, this.length);
        }
      },
    },

    mounted(){
      this.loadData()
    },

    methods : {
      computeDate(date){
        return Number(moment().subtract(date,'years').format("YYYY") )
      },

      onScroll ({ target: { scrollTop, clientHeight, scrollHeight }}) {
        if (scrollTop + clientHeight >= (scrollHeight - 20)) {
          this.loadMoreData()
        }
      },

      loadData(){
        this.showLoading = true
        axios({
          method : "GET",
          url : "https://api.spacexdata.com/v3/launches",
        }).then( ({data})=>{
          this.showLoading = false

          data.forEach(el => {
            el.expand = false
          });

          this.displayedData = data
        })
      },

      loadMoreData(){
        this.showLoading = true
        if (this.length == this.displayedData.length || this.length > this.displayedData.length){
          this.showLoading = false
          this.isEnd = true
          return;
        }else{
          this.length = this.length + 10;
        }
      }
    }
  }
</script>

<style lang="scss" scoped>
  .flex-center{
    display: block;
    width: auto;
    margin: 0 auto;
    text-align: center;
  }
</style>