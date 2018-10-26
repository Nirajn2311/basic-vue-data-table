<template>
<div>
  <div class="col-md-12"  v-if="users.length">
    <div class="form-group">
      <input type="text" class="form-control border border-info" v-model="search" placeholder="pretraži po svemu">
    </div>
    <div class="table-responsive">
      <table class="table table-striped table-bordered" style="width:100%">
          <thead width="400px">
              <tr>
                  <th scope="col">#</th>
                  <th scope="col" @click="sort('signatura')">Signatura<i class="fas fa-sort-alpha-down float-right"></i></th>
                  <th scope="col" @click="sort('autor')">Autor <i class="fas fa-sort-alpha-down float-right"></i></th>
                  <th scope="col" @click="sort('naslov')">Naslov <i class="fas fa-sort-alpha-down float-right"></i></th>
              </tr>
          </thead>
          <tbody>
              <tr v-for="(user, index) in (sortedActivity, filteredList)" :key="index">
                <td>{{index + 1}}</td>
                <td>{{signatura}}</td>
                <td>{{autor}}</td>
                <td>{{naslov}}</td>
              </tr>
          </tbody>
      </table>
    </div>
 <button @click="prevPage" class="float-left btn btn-outline-info btn-sm"><i class="fas fa-arrow-left"></i> Previous</button> 
 <button @click="nextPage" class="float-right btn btn-outline-info btn-sm">Next <i class="fas fa-arrow-right"></i></button>
  </div>
    </div>
</template>


<script>
/*eslint-disable*/
import axios from 'axios';

export default {
  data: () => ({
    users: [],
    currentSort:'name',
    currentSortDir:'asc',
    search: '',
    searchSelection: '',
    pageSize: 5,
    currentPage: 1
  }),

  methods:{
    sort:function(s) {
      if(s === this.currentSort) {
        this.currentSortDir = this.currentSortDir==='asc'?'desc':'asc';
      }
      this.currentSort = s;
    },
    nextPage:function() {
      if((this.currentPage*this.pageSize) < this.users.length) this.currentPage++;
    },
    prevPage:function() {
      if(this.currentPage > 1) this.currentPage--;
    }
  },

  computed: {
    sortedActivity:function() {

    return this.users.sort((a,b) => {
        let modifier = 1;
        if(this.currentSortDir === 'desc') modifier = -1;
        if(a[this.currentSort] < b[this.currentSort]) return -1 * modifier;
        if(a[this.currentSort] > b[this.currentSort]) return 1 * modifier;
        return 0;
      }).filter((row, index) => {
        let start = (this.currentPage-1)*this.pageSize;
        let end = this.currentPage*this.pageSize;
        if(index >= start && index < end) return true;
      });
    },

    filteredList () {
      return this.users.filter((data) => {
        let signatura = data.signatura.toLowerCase().match(this.search.toLowerCase());
        let autor = data.autor.toLowerCase().match(this.search.toLowerCase());
        let naslov = data.naslov.city.toLowerCase().match(this.search.toLowerCase());
        return signatura || autor|| naslov;
      }).filter((row, index) => {
        let start = (this.currentPage-1)*this.pageSize;
        let end = this.currentPage*this.pageSize;
        if(index >= start && index < end) return true;
      });
    }
  },

  created () {
    return axios.get('https://gist.githubusercontent.com/diomed/1d6fe143db4f2d6a3c16ac9d47d95193/raw/23a5f90d7755c380196b3b2860abe6ec0e9c7790/library.json')
      .then(response => {
        this.users = response.data
      })
  },

}
</script>

<style>
th {
  cursor:pointer;
  /* width: 500px !important; */
  white-space: nowrap;
}

tr {
  white-space: nowrap;
}

</style>
