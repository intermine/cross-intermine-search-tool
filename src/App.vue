<template>
  <v-app id="inspire">
    <v-navigation-drawer
      :clipped="$vuetify.breakpoint.lgAndUp"
      v-model="drawer"
      fixed
      app
    >
      <v-list dense>
        <template>
          <v-list-tile @click="activateHomeTab()">
            <v-list-tile-action>
              <v-icon color="blue-grey darken-1">home</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title>
                Home
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>

          <v-list-tile @click="getLocalStorage()" id="fav">
            <v-list-tile-action>
              <v-icon color="blue-grey darken-1">library_books</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title>
                Saved Items
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>

          <v-list-tile @click="activateInterMinesTab()" id="exploreIM">
            <v-list-tile-action>
              <v-icon color="blue-grey darken-1">apps</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title>
                Explore InterMines
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>

          <v-list-group
            v-model="selectIntermines.model"
            :prepend-icon="selectIntermines.model ? selectIntermines.icon : selectIntermines['icon-alt']"
            append-icon=""
            id="minesBox"
          >
            <v-list-tile slot="activator">
              <v-list-tile-content>
                <v-list-tile-title>
                  {{ selectIntermines.text }}
                </v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>

            <v-list-tile @click="selectAll">
              <v-list-tile-action>
                <v-icon color="blue-grey darken-1">done_all</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title>
                  Select All / None
                </v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>

            <template v-for="(child, i) in neighbourhoods">
              <v-list-tile @click="changeNeighbourhood(child)" :key="i">
                <v-list-tile-action>
                  <v-icon color="blue-grey darken-1">image_search</v-icon>
                </v-list-tile-action>
                <v-list-tile-content>
                  <v-list-tile-title>
                    Only {{child}}
                  </v-list-tile-title>
                </v-list-tile-content>
              </v-list-tile>
            </template>

            <v-list-tile
              v-for="(child, i) in selectIntermines.children"
              :key="i"
              @click=""
            >
              <v-list-tile-action>
              </v-list-tile-action>

              <v-list-tile-content>
                <v-list-tile-title>
                  <v-checkbox :ripple="false" v-model="selected" :label="child.text" color="blue-grey darken-1" :value="child"></v-checkbox>
                </v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
          </v-list-group>

          <v-radio-group v-model="scoreFilter" @change="filterResults" id="relevanceBox">
            <v-list-tile>
              <v-list-tile-action>
                <v-icon color="blue-grey darken-1">star</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title>
                  Relevance Score
                </v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>

            <v-list-tile>
              <v-list-tile-action>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title>
                  <v-radio :ripple="false" label="4 stars & up" value="4" color="blue-grey darken-1" ></v-radio>
                </v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile>
              <v-list-tile-action>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title>
                  <v-radio :ripple="false" label="3 stars & up" value="3" color="blue-grey darken-1" ></v-radio>
                </v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile>
              <v-list-tile-action>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title>
                  <v-radio :ripple="false" label="2 stars & up" value="2" color="blue-grey darken-1" ></v-radio>
                </v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile>
              <v-list-tile-action>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title>
                  <v-radio :ripple="false" label="1 star & up" value="1" color="blue-grey darken-1" ></v-radio>
                </v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
          </v-radio-group>

          <v-list-tile id="categoryBox">
            <v-list-tile-action>
              <v-icon color="blue-grey darken-1">view_list</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title>
                Category Filter
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>

          <v-list-tile @click="selectAllNoneCategories" v-show="category.length != 0">
            <v-list-tile-action>
              <v-icon color="blue-grey darken-1">done_all</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title>
                Select All / None
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>

          <v-list-tile v-if="category.length == 0">
            <v-list-tile-action>
              <v-icon></v-icon>
            </v-list-tile-action>

            <v-list-tile-content>
              <v-list-tile-title>
                --EMPTY--
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>

          <v-list-tile
            v-if="category.length != 0"
            v-for="(categoryItem, i) in category"
            :key="i"
            @click=""
          >
            <v-list-tile-action>
              <v-icon :style="{ color: selectColor(categoryItem)}">layers</v-icon>
            </v-list-tile-action>

            <v-list-tile-content>
              <v-list-tile-title>
                <v-checkbox :ripple="false" v-model="categoryFilters" :label="categoryItem" :value="categoryItem" color="blue-grey darken-1"></v-checkbox>
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>

          <v-list-tile @click="" :href="'http://' + host + '/docs'" target="_blank">
            <v-list-tile-action>
              <v-icon color="blue-grey darken-1">book</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title>
                Documentation
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>

          <v-list-tile @click="" href="http://chat.intermine.org/" target="_blank">
            <v-list-tile-action>
              <v-icon color="blue-grey darken-1">chat_bubble</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title>
                Chat with us!
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>

          <v-list-tile @click="tabModal = 'tab-home'" id="tourBtn">
            <v-list-tile-action>
              <v-icon color="blue-grey darken-1">help</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title>
                Feature Tour
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>

        </template>
      </v-list>
    </v-navigation-drawer>


    <v-toolbar
      :clipped-left="$vuetify.breakpoint.lgAndUp"
      color="teal darken-3"
      dark
      app
      fixed
    >
      <v-toolbar-title style="width: 18em" class="ml-0 pl-3" id="app_title">
        <v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon>
        <span class="hidden-sm-and-down">Cross InterMine Search Tool</span>
      </v-toolbar-title>
      <v-layout row align-center style="max-width: 650px" id="searchBox">
        <v-text-field
          single-line
          hide-details
          flat
          solo-inverted
          prepend-icon="search"
          label="Search"
          @keyup.enter="searchMine"
          v-model="searchTerm"
        ></v-text-field>
      </v-layout>
      <v-spacer></v-spacer>
      <v-btn icon large id="tourBtn1" @click="tabModal = 'tab-home'">
        <v-avatar size="2em" tile>
          <v-icon>help</v-icon>
        </v-avatar>
      </v-btn>
      <v-btn icon large href="http://intermine.org/" target="_blank">
        <v-avatar size="2em" tile>
          <img
            src="/static/assets/logo.png"
            alt="IM"
          >
        </v-avatar>
      </v-btn>
    </v-toolbar>

    <template v-if="modalData != null">
      <v-dialog v-model="dialog" max-width="60%" scrollable>
        <v-card>
          <v-card-title class="headline">
            <strong> Type - {{ modalData.type }}&nbsp;</strong>
            <small>| Relevance Score &nbsp;</small>
            <template v-for="searchPoints in calculateSearchPoints(modalData.relevance)">
              <v-icon small color="yellow" :key="searchPoints + '_active'">star</v-icon>
            </template>
          </v-card-title>

          <v-card-text>
            <template v-for="(modalDataField, key, j) in modalData.fields">
              <span :key="j"><strong>{{ key.toUpperCase() }}</strong> - {{ modalDataField }} </span><br>
            </template>
          </v-card-text>
          <v-card-actions>
            <v-btn
              dark
              :ripple="false"
              target="_blank"
              color="green "
              :href="generateReportLink(modalData.id, modalData.url)"
            >
              <v-icon color="white">open_in_new</v-icon> Open Portal
            </v-btn>
            <v-spacer></v-spacer>
            <v-btn color="green darken-1" flat @click.native="dialog = false">Close</v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
    </template>

    <template>
      <div class="text-xs-center">
        <v-dialog
          v-model="progressDialog"
          persistent
          width="20%"
        >
          <v-card
            color="white"
            light
          >
            <v-card-text>
              Please wait
              <v-progress-linear
                indeterminate
                color="purple"
                class="mb-0"
              ></v-progress-linear>
            </v-card-text>
          </v-card>
        </v-dialog>
      </div>
    </template>

    <v-content>
      <v-tabs
        dark
        color="teal darken-1"
        grow
        v-model="tabModal"
      >
        <v-tabs-slider color="yellow"></v-tabs-slider>

        <template>
          <v-tab
            :href="'#tab-home'"
            :ripple="false"
            >
            Home
          </v-tab>
          <v-tab
            :href="'#tab-results'"
            :ripple="false"
            v-if="searchActive"
          >
            Results
          </v-tab>
          <v-tab
            :href="'#tab-localstorage'"
            :ripple="false"
            v-if="localStorageActive"
          >
            Saved Items
          </v-tab>
          <v-tab
            :href="'#tab-intermines'"
            :ripple="false"
            v-if="interminesActive"
          >
            InterMines
          </v-tab>
        </template>

        <v-tabs-items fixed>
          <v-tab-item
            :id="'tab-home'"
          >
            <template>
              <div
                id="e3"
                style="margin: auto;"
                class="grey lighten-3"
              >
                <v-card>
                  <v-container
                    fluid
                    style="min-height: 0;"
                    grid-list-lg
                  >
                    <v-layout row wrap>
                      <v-flex xs12>
                        <v-card color="green darken-1" dark raised height="100%">
                          <v-container fluid grid-list-lg>
                            <v-layout row>
                              <v-flex>
                                <v-card-media
                                  src="https://cdn.rawgit.com/intermine/design-materials/f5f00be4/logos/intermine/intermine.png"
                                  height="4em"
                                  contain
                                ></v-card-media>
                                <br>
                                <div>
                                  <h1>Cross InterMine Search Tool</h1><br>
                                  <p style="font-size: larger;">Select the InterMines you are interested in; and type a search keyword or symbol into the searchbar up the top and hit enter.
                                    If you're not sure what InterMines to choose or what to search, check out the results for <span class="example" @click="exampleSearch('adh')">ADH</span>, <span class="example" @click="exampleSearch('brca1')">BRCA1</span> or <span class="example" @click="exampleSearch('gata1')">GATA1</span></p>
                                </div>
                              </v-flex>
                            </v-layout>
                          </v-container>
                        </v-card>
                      </v-flex>
                    </v-layout>
                  </v-container>
                </v-card>
              </div>
            </template>

          </v-tab-item>

          <v-tab-item
            :id="'tab-intermines'"
          >
            <template>
              <div
                id="e3"
                style="margin: auto;"
                class="grey lighten-3"
              >
                <v-card>
                  <v-container
                    fluid
                    grid-list-lg
                  >
                    <v-layout row wrap>
                      <v-flex xs12 v-for="(item, i) in minesList" :key="i">
                        <v-card color="grey lighten-5" light v-bind:href="''+item.url+''" target="_blank">
                          <v-container fluid grid-list-lg>
                            <v-layout row>
                              <v-flex xs10>
                                <div>
                                  <div class="headline"><strong>{{ item.name }}</strong></div>
                                  <div>{{ item.description }}</div>
                                  <div>
                                    <strong>Organisms - </strong><span v-for="organism in item.organisms" :key="organism"> {{ organism }} /</span>
                                  </div>
                                  <br>
                                  <div style="font-style: italic;">{{ item.url }}</div>
                                </div>
                              </v-flex>
                              <v-flex xs2 style="align-self: center">
                                <v-card-media
                                  v-if="item.images !== undefined && Object.keys(item.images).indexOf('logo') >= 0"
                                  :src="item.images['logo']"
                                  height="4em"
                                  contain
                                ></v-card-media>
                                <v-card-media
                                  v-else
                                  src="/static/assets/logo.png"
                                  height="4em"
                                  contain
                                ></v-card-media>
                              </v-flex>
                            </v-layout>
                          </v-container>
                        </v-card>
                      </v-flex>
                    </v-layout>
                  </v-container>
                </v-card>
              </div>
            </template>
          </v-tab-item>

          <v-tab-item
            :id="'tab-localstorage'"
          >
            <template v-if="localData.length != 0">
              <v-list two-line subheader>
                <template>
                  <v-list-tile
                    v-for="(item, i) in localData"
                    :key="i"
                    avatar
                    @click="showModal(item, item.url, item.mineName)"
                  >
                    <v-list-tile-avatar>
                      <strong>{{ i+1 }}</strong>
                    </v-list-tile-avatar>
                    <v-list-tile-content>
                      <v-list-tile-title :style="{ borderLeft: 'solid 4px ' + selectColor(item.type) }">
                        <strong> &nbsp  {{ item.mineName }} | Type - {{ item.type }} </strong>
                        <v-tooltip bottom>
                          <v-icon
                            slot="activator"
                            @click.stop="deleteFromLocalStorage(item.localId)"
                            :style="{ color: selectColor(item.type)}"
                          >
                            delete
                          </v-icon>
                          <span>Delete from Saved Items</span>
                        </v-tooltip>
                        <small><strong>| Relevance Score &nbsp;</strong></small>
                        <template v-for="searchPoints in calculateSearchPoints(item.relevance)">
                          <v-icon small color="yellow" :key="searchPoints + '_active'">star</v-icon>
                        </template>
                      </v-list-tile-title>
                      <v-list-tile-sub-title style="text-overflow: ellipsis;">
                        <template v-for="(mineResultsField, key, j) in item.fields">
                          <span :key="j"><strong>| {{ key.toUpperCase() }}</strong> - {{ mineResultsField }} </span>
                        </template>
                      </v-list-tile-sub-title>
                    </v-list-tile-content>
                    <v-list-tile-action>
                      <v-btn
                        icon
                        ripple
                        target="_blank"
                        color="orange lighten-2"
                        @click.stop
                        :href="generateReportLink(item.id, item.url)"
                      >
                        <v-icon color="white">open_in_new</v-icon>
                      </v-btn>
                    </v-list-tile-action>
                  </v-list-tile>
                </template>
              </v-list>
            </template>
            <template v-else>
              No Saved Items found
            </template>
          </v-tab-item>

          <v-tab-item
            :id="'tab-results'"
          >

            <template>
              <v-alert v-if="this.protocol === 'https:'" :value="true" type="warning">
                You're currently viewing the HTTPS website. Due to security limitations, we are unable to show results from HTTP-only InterMines. You may be able to see more results if you reload this site via HTTP - [<a style="color: inherit;" :href="`http://${this.host}`">{{ `http://${this.host}` }}</a>].
              </v-alert>
              <v-alert v-if="this.failedSearchMines.length != 0" :value="true" id="error" type="error">
                Something went wrong in the following mine(s): <span v-for="errorMine in this.failedSearchMines" :key="errorMine"> {{ errorMine }} /</span>
              </v-alert>
              <v-alert v-if="this.emptyResultMines.length != 0" :value="true" color="indigo lighten-4">
                0 results returned from these mines: <span v-for="emptyMine in this.emptyResultMines" :key="emptyMine"> {{ emptyMine }} /</span>
              </v-alert>

              <v-card
                v-for="(selectedMine, i) in this.selected"
                :key="i"
              >
                <v-container
                  fluid
                  style="min-height: 0;"
                  grid-list-lg
                  v-if="emptyResultMines.indexOf(selectedMine.text) < 0 && failedSearchMines.indexOf(selectedMine.text) < 0 && selectedMine.result != undefined"
                >
                  <v-layout row>
                    <v-flex xs12>
                      <v-toolbar color="blue-grey darken-1" dark flat>
                          <v-toolbar-title>{{ selectedMine.text }}&nbsp</v-toolbar-title>
                          <span v-if="selectedMine.result != undefined && selectedMine.result.totalHits != undefined">
                             <strong>
                               <small>
                                ({{ selectedMine.result.totalHits }} results)
                              </small>
                             </strong>
                          </span>
                      </v-toolbar>
                      <v-card style="overflow-y: auto; max-height: 350px; height: auto;">

                        <v-list two-line subheader>

                          <template v-if="selectedMine.result == undefined">
                            <h3 style="text-align: center;">Loading...</h3>
                          </template>
                          <template v-else-if="filterResults(selectedMine.result.results).length == 0">
                            <h3 style="text-align: center;">No results were found for the selected filters. Please remove filters to view results.</h3>
                          </template>

                          <template v-else>
                            <v-list-tile
                              v-for="(mineResults, i) in filterResults(selectedMine.result.results)"
                              :key="i"
                              avatar
                              @click="showModal(mineResults, selectedMine.url, selectedMine.text)"
                            >
                              <v-list-tile-avatar>
                                <strong>{{ i+1 }}</strong>
                              </v-list-tile-avatar>
                              <v-list-tile-content>
                                <v-list-tile-title :style="{ borderLeft: 'solid 4px ' + selectColor(mineResults.type) }">
                                  <strong> &nbsp {{ mineResults.type }} </strong>
                                  <v-tooltip bottom>
                                    <v-icon
                                      slot="activator"
                                      @click.stop="saveToLocalStorage(mineResults, selectedMine.text, selectedMine.url)"
                                      :style="{ color: selectColor(mineResults.type)}"
                                    >
                                      library_books
                                    </v-icon>
                                    <span>Add to Saved Items</span>
                                  </v-tooltip>
                                  <small><strong>| Relevance Score &nbsp;</strong></small>
                                  <template v-for="searchPoints in calculateSearchPoints(mineResults.relevance)">
                                    <v-icon small color="yellow" :key="searchPoints + '_active'">star</v-icon>
                                  </template>
                                  <!-- <template v-for="searchPoints in (5 - calculateSearchPoints(mineResults.relevance))">
                                    <v-icon small color="grey lighten-1" :key="searchPoints + '_inactive'">star</v-icon>
                                  </template> -->
                                </v-list-tile-title>
                                <v-list-tile-sub-title style="text-overflow: ellipsis;">
                                  <template v-for="(mineResultsField, key, j) in mineResults.fields">
                                    <span :key="j"><strong>| {{ key.toUpperCase() }}</strong> - {{ mineResultsField }} </span>
                                  </template>
                                </v-list-tile-sub-title>
                              </v-list-tile-content>
                              <v-list-tile-action>
                                <v-btn
                                  icon
                                  ripple
                                  target="_blank"
                                  color="blue-grey lighten-2"
                                  @click.stop
                                  :href="generateReportLink(mineResults.id, selectedMine.url)"
                                >
                                  <v-icon color="white">open_in_new</v-icon>
                                </v-btn>
                              </v-list-tile-action>
                            </v-list-tile>
                            <template v-if="selectedMine.result.fetchMore != undefined && selectedMine.result.fetchMore === true">
                              <h3 :id="'loadMsg_' + selectedMine.text" style="text-align: center; cursor: pointer;" @click="loadMoreResults(selectedMine.result.searchTerm, selectedMine)">
                                Load more
                              </h3>
                            </template>
                          </template>
                        </v-list>
                      </v-card>
                    </v-flex>
                  </v-layout>
                </v-container>
              </v-card>
            </template>

          </v-tab-item>
        </v-tabs-items>
      </v-tabs>
    </v-content>
  </v-app>
</template>

<script>
  import axios from 'axios'

  /**
   * @module Cross-InterMine-Search-Tool
   * @vue-data {Array} [localData= (empty array)] - Used to store the local storage data (Saved Items) for rendering
   * @vue-data {Boolean} [toggleSelectCategory=true] - Toggle flag for Select All/None in Categories section
   * @vue-data {Boolean} [toggleSelectMines=false] - Toggle flag for Select All/None in Select Mines section
   * @vue-data {Boolean} [localStorageActive=false] - Toggle flag for activating the 'Saved Items' tab
   * @vue-data {Boolean} [interminesActive=false] - Toggle flag for activating the 'Explore InterMines' tab
   * @vue-data {Boolean} [progressDialog=true] - Flag for activating the 'Progress Loading' dialog on page load
   * @vue-data {Boolean} [dialog=false] - Toggle flag for activating the 'Result popup' modal when a search result is clicked
   * @vue-data {Boolean} [drawer=null] - Toggle flag for activating the side navigation drawer
   * @vue-data {String} [tabModal='tab-home'] - Toggle flag for activating the tabs when they are clicked
   * @vue-data {String} [searchTerm=''] - It stores the current search term
   * @vue-data {Object} [selectIntermines={ }] - It contains the metadata and the complete list of InterMines fetched from the Registry
   * @vue-data {Number} [scoreFilter=1] - It is used to store the 'Relevance Points' filter value
   * @vue-data {Array} [selected=(empty array)] - It is used to store the list of selected InterMines for searching
   * @vue-data {Array} [category=(empty array)] - It is used to store the list of all the categories present in a Search instance
   * @vue-data {Array} [categoryFilters=(empty array)] - It is used to store the list of selected categories for filtering the results
   * @vue-data {Boolean} [searchActive=false] - Flag for activating the results tab
   * @vue-data {Array} [emptyResultMines=(empty array)] - It is the list of all those InterMines which do not have any results
   * @vue-data {Array} [failedSearchMines=(empty array)] - It is the list of all those InterMines which result in a failure during the search process
   * @vue-data {String} [protocol=document.location.protocol] - Stores the name of the protocol used (http/https)
   * @vue-data {String} [host=document.location.host] - Stores the name of the host
   * @vue-data {Object} [modalData=null] - Stores the result data for the result modal popup
   * @vue-data {Object} [minesList=null] - Stores the metadata for InterMine instances fetched from the Registry (for 'Explore InterMines' section)
   * @vue-data {Array} [neighbourhoods=(empty array)] - It contains the set of all neighbourhoods which are present in the InterMine registry
   */

  export default {
    data: () => ({
      localData: [],
      toggleSelectCategory: true,
      toggleSelectMines: false,
      localStorageActive: false,
      interminesActive: false,
      progressDialog: true,
      dialog: false,
      drawer: null,
      tabModal: 'tab-home',
      searchTerm: '',
      selectIntermines: {
        icon: 'keyboard_arrow_up',
        'icon-alt': 'keyboard_arrow_down',
        text: 'Select InterMines',
        model: true,
        children: []
      },
      scoreFilter: '1',
      selected: [],
      category: [],
      categoryFilters: [],
      searchActive: false,
      emptyResultMines: [],
      failedSearchMines: [],
      protocol: document.location.protocol,
      host: document.location.host,
      modalData: null,
      minesList: null,
      neighbourhoods: []
    }),
    methods: {
      /**
       * This function is responsible for the initiation of search process.
       * It gets executed when the user enters a search term and hits Enter.
       * @yields {Array} The InterMine QuickSearch APIs return the search results for all the selected mines and is then pushed to the relevant data variables.
       */
      searchMine () {
        let vm = this
        if (vm.searchTerm.trim() === '' || vm.selected.length === 0) {
          alert('Please select a mine and use a search term to search the mines.')
          return
        }
        vm.$router.replace({'query': {'search': vm.searchTerm}})
        vm.failedSearchMines = []
        vm.emptyResultMines = []
        vm.searchActive = true
        vm.tabModal = 'tab-results'
        vm.selectIntermines.model = false
        vm.category = []
        vm.categoryFilters = []
        vm.selected.map((mineObj) => {
          mineObj.result = undefined
          let mineService = new intermine.Service({root: mineObj.url}) // eslint-disable-line
          let options = {
            q: vm.searchTerm
          }
          mineService.search(options).then((data) => {
            if (data !== undefined && data.wasSuccessful === true && data.statusCode === 200 && data.error === null) {
              mineObj.result = data
              mineObj.result.searchTerm = vm.searchTerm
              if (data.results.length === 0) {
                vm.emptyResultMines.push(mineObj.text)
              }
              if (data.results.length === 100) {
                mineObj.result.fetchMore = true
              } else {
                mineObj.result.fetchMore = false
              }
              vm.pushToCategoryList(data.facets.Category)
            } else {
              vm.failedSearchMines.push(mineObj.text)
            }
            vm.$forceUpdate()
          }, (errorText) => {
            console.log(errorText)
            vm.failedSearchMines.push(mineObj.text)
            vm.$forceUpdate()
          })
        })
      },
      /**
       * This function is used to select all / none InterMines for running the search on them.
       * It gets executed when the user hits the 'Select All/None' button in the InterMines checkbox section.
       * @yields {Array} It pushes (or removes) all the InterMines to the list containing the selected InterMines.
       */
      selectAll () {
        let vm = this
        if (vm.toggleSelectMines) {
          this.selected = []
        } else {
          vm.selected = []
          vm.selectIntermines.children.map((item) => {
            vm.selected.push(item)
          })
        }
        vm.toggleSelectMines = !vm.toggleSelectMines
      },
      /**
       * This function is used to select all / none Categories after running the search process.
       * It gets executed when the user hits the 'Select All/None' button in the Categories filter section.
       * @yields {Array} It pushes (or removes) all the Categories to the list containing the selected Categories.
       */
      selectAllNoneCategories () {
        let vm = this
        if (vm.toggleSelectCategory) {
          vm.categoryFilters = []
        } else {
          vm.categoryFilters = []
          vm.category.map((item) => {
            vm.categoryFilters.push(item)
          })
        }
        vm.toggleSelectCategory = !vm.toggleSelectCategory
      },
      /**
       * It calculates the relevance rating (on a scale of 5) based on the relevance score of the result item for a particular search.
       * It uses a logical formula to get to the resultant value.
       * @param {Float} score - Relevance score as returned by th Search API
       * @returns {Number} The final relevance rating on a scale of 5
       */
      calculateSearchPoints (score) {
        return Math.round(Math.max(0.1, Math.min(1, score)) * 5)
      },
      /**
       * It generates the report / portal link for a particular result item..
       * The link is generated by using the InterMine URL and the ID of the result item.
       * @param {Number} id - InterMine Search API returns a unique ID for every result item (ID is used for the purpose of indexing in the various InterMine data warehouses)
       * @param {String} url - Every InterMine instance has a unique base URL
       * @returns {String} The final generated portal link for the given item
       */
      generateReportLink (id, url) {
        return url + '/report.do?id=' + id
      },
      /**
       * This function is used for filtering the search results.
       * The search results are mainly filtered based on their Category and their Relevance Score.
       * The filtering is done by using the Array Filter operation of JavaScript.
       * @param {Array} data - The array of search result items is passed into the function as a parameter
       * @returns {Array} The final filtered array of results is returned after applying the filter operation.
       */
      filterResults (data) {
        let vm = this
        if (!Array.isArray(data)) return data
        return data.filter((resultItem) => {
          return (vm.calculateSearchPoints(resultItem.relevance) >= vm.scoreFilter) && (vm.categoryFilters.indexOf(resultItem.type) >= 0)
        })
      },
      /**
       * This function maps various genomic categories with their InterMine color coding.
       * It is used at various places throughout the application so as to differentiate between various categories.
       * @param {String} dataType - The name of the data-type or category is passed as a parameter into this function
       * @returns {String} The function returns the InterMine color coding for that category
       */
      selectColor (dataType) {
        switch (dataType) {
          case 'Genes':
          case 'Genomics':
          case 'genes':
          case 'gene':
          case 'homologues':
          case 'Homologue':
          case 'Homology':
          case 'Genome':
          case 'Gene': return '#8BC34A'
          case 'Proteins':
          case 'proteins':
          case 'ProteinDomain':
          case 'Protein': return '#FFC107'
          case 'publications':
          case 'Author':
          case 'Literature':
          case 'Publication': return '#2196F3'
          case 'Organism': return '#E91E63'
          case 'interactions':
          case 'InteractionTerm':
          case 'InteractionExperiment':
          case 'Complex':
          case 'Interactions':
          case 'Interaction': return '#FF5722'
          case 'GOTerm':
          case 'goAnnotation':
          case 'GOAnnotation': return '#9C27B0'
          default: return '#cccccc'
        }
      },
      /**
       * This function is used to push unique categories to the category list.
       * It helps in filtering and keeping track of various categories during search.
       * @param {Object} categoryObj - It is the object returned by every InterMine instance in response to the Search API
       * @yields {Array} The array consists of all the unique categories returned by the Search APIs
       */
      pushToCategoryList (categoryObj) {
        let vm = this
        let categoryArray = Object.keys(categoryObj)
        categoryArray.forEach((item) => {
          if (vm.category.indexOf(item) < 0) {
            vm.category.push(item)
          }
          if (vm.categoryFilters.indexOf(item) < 0) {
            vm.categoryFilters.push(item)
          }
        })
        vm.category.sort()
      },
      /**
       * This function is a demo implementation of the search tool.
       * It triggers an automatic search for a given example keyword in all InterMine instances.
       * @param {String} term - It is the example search term which is used to search in all the InterMine instances
       * @yields {Array} All the InterMine instances return results.
       */
      exampleSearch (term) {
        this.searchTerm = term
        this.toggleSelectMines = false
        this.selectAll()
        this.searchMine()
      },
      /**
       * This function is used to implement the concept of neighbourhoods in the Search Tool.
       * All the InterMines with a given neighbourhood are automatically selected.
       * @param {String} neighbourhood - It is a concept implemented in InterMine. Neighbourhoods are mainly of two types - MODs & Plants
       * @yields {Array} Function gives an array of the InterMines of a particular neighbourhood.
       */
      changeNeighbourhood (neighbourhood) {
        let vm = this
        vm.selected = []
        vm.selectIntermines.children.map((item) => {
          if (item.neighbourhood.indexOf(neighbourhood) >= 0) {
            vm.selected.push(item)
          }
        })
      },
      /**
       * This function is used to generate the modal popup for every search result item.
       * Every popup is generated dynamically by passing values into a modal template.
       * @param {String} data - The result item data is passed to the function as a parameter
       * @param {String} url - The url of the InterMine instance is also passed as a parameter
       * @param {String} mineName - The name of the InterMine instance is also passed as a parameter
       * @yields {Modal} This function builds a modal popup for every result item, which appears on click event over a result item.
       */
      showModal (data, url, mineName) {
        data.url = url
        data.mineName = mineName
        this.modalData = data
        this.dialog = true
      },
      /**
       * The Quick Search API endpoint of InterMine returns a maximum of 100 result items on one API hit.
       * This function is used to load more results from the InterMine Search API.
       * @param {String} searchTerm - The search term for which more results are to be fetched
       * @param {Object} selectedMine - The details of the mine from where the results are to be fetched
       * @yields {Array} The array containing the search results is updated with more items
       */
      loadMoreResults (searchTerm, selectedMine) {
        document.getElementById('loadMsg_' + selectedMine.text).innerHTML = 'Loading...'

        let vm = this
        let mineService = new intermine.Service({root: selectedMine.url}) // eslint-disable-line
        let mineIndex = vm.selected.indexOf(selectedMine)

        let options = {
          q: searchTerm,
          start: vm.selected[mineIndex].result.results.length
        }

        mineService.search(options).then((data) => {
          if (data !== undefined && data.wasSuccessful === true && data.statusCode === 200 && data.error === null) {
            data.results.map((item) => {
              vm.selected[mineIndex].result.results.push(item)
            })
            if (data.results.length === 100) {
              vm.selected[mineIndex].result.fetchMore = true
            } else {
              vm.selected[mineIndex].result.fetchMore = false
            }
            vm.$forceUpdate()
          }
          document.getElementById('loadMsg_' + selectedMine.text).innerHTML = 'Load more'
        })
      },
      /**
       * This function is used to save data to the local storage.
       * In our case, the results are added to Saved Items and eventually are saved to the local storage.
       * @param {String} mineName - Name of the InterMine instance
       * @param {String} url - URL of the InterMine instance
       * @param {Object} data - The result data object which is saved to the local storage
       */
      saveToLocalStorage (data, mineName, url) {
        let vm = this
        data.mineName = mineName
        data.url = url
        let localId = `IM_${mineName}_${data.id}`
        data.localId = localId
        if (localStorage.getItem(localId)) {
          alert('Item already exists!')
        } else {
          localStorage.setItem(localId, JSON.stringify(data))
          vm.refreshLocalData()
          alert('Added to Saved Items!')
        }
      },
      /**
       * This function triggers the process of fetching data from local storage.
       * It also activates the 'Saved Items' tab.
       */
      getLocalStorage () {
        let vm = this
        vm.localStorageActive = true
        vm.tabModal = 'tab-localstorage'
        vm.refreshLocalData()
      },
      /**
       * This function activates the 'Explore InterMines' tab.
       */
      activateInterMinesTab () {
        let vm = this
        vm.interminesActive = true
        vm.tabModal = 'tab-intermines'
      },
      /**
       * This function activates the 'Home' tab.
       */
      activateHomeTab () {
        let vm = this
        vm.tabModal = 'tab-home'
      },
      /**
       * This function is used to load / refresh the local storage data, i.e. FAVOURITE items data, and push it to a data property in order to render on the webpage.
       * @yields {Array} The array containing the local storage items or FAVOURITE items
       */
      refreshLocalData () {
        let vm = this
        vm.localData = []
        for (let item in localStorage) {
          if (item.substring(0, 2) === 'IM') {
            let dataItem = JSON.parse(localStorage.getItem(item))
            vm.localData.push(dataItem)
          }
        }
      },
      /**
       * This function is used to delete the local storage data, i.e. FAVOURITE items data.
       * @yields {Array} After the deletion of an item from the local storage, the array containing the local storage data is updated
       */
      deleteFromLocalStorage (localId) {
        localStorage.removeItem(localId)
        this.refreshLocalData()
      },
      /**
       * This function runs during the created() lifehook of VueJS. It is activated and executed during the creation of views.
       * This function fetches the list of InterMines from the InterMine Registry API endpoint and pushes it to an array for further usage.
       * It also takes input query parameters from the URL.
       * @param {String} search - URL query parameter which acts as the search term
       * @yields {Array} The array containing the details of each InterMine instance
       * @yields It also allows us to use the search tool by passing query parameters from any other external website
       */
      onCreated () {
        let vm = this
        axios.get(`https://registry.intermine.org/service/instances`)
          .then(response => {
            vm.minesList = response.data.instances
            this.neighbourhoods = []
            response.data.instances.map((mine) => {
              mine.neighbours.map((neighbourhoodItem) => {
                if (this.neighbourhoods.indexOf(neighbourhoodItem) === -1) {
                  this.neighbourhoods.push(neighbourhoodItem)
                }
              })
              this.selectIntermines.children.push({
                text: mine.name,
                url: mine.url,
                neighbourhood: mine.neighbours
              })
            })
          }).then(() => {
            this.selectAll()
            vm.progressDialog = false
            if (this.$route.query.search) {
              this.searchTerm = this.$route.query.search
              this.searchMine()
            }
          })
      }
    },
    created () {
      this.onCreated()
    }
  }
</script>

<style>
  @media screen and (max-width: 600px) {
    #app_title {
      width: 0em;
    }
  }
  
  #error {
     background-color: #FF9494 !important;
  }

  .example {
    font-weight: bold;
    font-style: italic;
    cursor: pointer;
    text-decoration: underline;
  }

  .mine_link {
    text-decoration: inherit;
    color: inherit;
    cursor: pointer;
  }
</style>
