<template>
    <div class="content">
        <table>
            <thead>
                <tr>
                    <th @click="sort(column.dataKey)" v-for="column in columns" :key="column.dataKey">
                        {{ column.name }}
                    </th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="row in sortedRows" :key="row.code">
                    <td>
                        {{ row.code }}
                    </td>
                    <td>
                        {{ columns[1].formatValue(row.startDate) }}
                    </td>
                    <td>
                        {{ columns[2].formatValue(row.sales) }}
                    </td>
                </tr>
            </tbody>
        </table>
        <p>
          <button @click="prevPage">Previous</button> 
          <button @click="goToPage(index+1)" v-for="(row, index) in numberOfPages" :key="row.code">{{ index+1 }}</button>
          <button @click="nextPage">Next</button>
          <span>
              {{ numberOfPages }} pages  ({{ rows.length }} results)
          </span>
        </p>
    </div>
</template>

<script>
    export default {
        name: "v-data-table",
        props: ["columns", "rows", "perPage"],
        data() {
            return {
                currentSort:'code',
                currentSortDir:'asc',
                pageSize: this.perPage,
                currentPage: 1,
                numberOfPages: Math.ceil(this.rows.length/this.perPage)
            }
        },
        methods:{
          sort:function(s) {
            if(s === this.currentSort) {
              this.currentSortDir = this.currentSortDir==='asc'?'desc':'asc';
            }
            this.currentSort = s;
          },
          goToPage:function(index) {
            this.currentPage = index;
          },
          nextPage:function() {
            if((this.currentPage*this.pageSize) < this.rows.length) this.currentPage++;
          },
          prevPage:function() {
            if(this.currentPage > 1) this.currentPage--;
          }
        },
        computed:{
          sortedRows:function() {
            return this.rows.slice().sort((a,b) => {
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
        }
    }
</script>

<style>
  .content {
    padding: 10px;
    background-color: #f9f9f9;
  }
  .content table {
    border-collapse: collapse;
    width: 100%;
    color: #757575;
    background-color: #f9f9f9;
  }

  .content table td, .content table th {
    padding: 14px;
  }

  .content table tbody tr:nth-child(odd){background-color: #d6d6d6;}

  .content table tr:hover {background-color: #bfbfbf;}

  .content table th {
    padding-top: 12px;
    padding-bottom: 12px;
    text-align: left;
  }
</style>