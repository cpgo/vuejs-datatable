<script>
  export default {
    props: {
      url: String,
      columns: {},
      pagination_numbers: {
        type: Array,
        default: () => [5, 10, 25, 50]
      }
    },
    data () {
      return {
        pagination_data: {},
        response_data: {},
        sortByKey: '',
        sortOrder: 1,
        colaboradores: {},
        search_param: '',
        ajax_params: {per_page: this.per_page || 25}
      }
    },
    methods: {
      sortKey (key) {
        this.sortOrder = this.sortOrder * -1
        this.sortByKey = key
      },
      ajaxRequest (url, data = {}) {
        this.$http({url: url, data, method: 'GET'}).then((response) => {
          this.pagination_data = response.data
          this.response_data = response.data.data
        })
      }
    },
    created () {
      this.ajaxRequest(this.url, this.ajax_params)
    }
  }
</script>

<template>
  <nav>
    <ul class="pagination">
      <li>
        <a class="btn btn-default" @click="ajaxRequest(pagination_data.url, ajax_params)">
          Primeira
        </a>
      </li>

      <li>
        <a class="btn btn-default" @click="!pagination_data.prev_page_url? '' : ajaxRequest(pagination_data.prev_page_url, ajax_params)"
                :disabled="!pagination_data.prev_page_url">
          Anterior
        </a>
      </li>
      <li class="active">
        <span>{{pagination_data.current_page}}</span>
      </li>
      <li>
        <a class="btn btn-default" @click="!pagination_data.next_page_url? '' : ajaxRequest(pagination_data.next_page_url, ajax_params)"
                :disabled="!pagination_data.next_page_url">
          Proxima
        </a>
      </li>

      <li>
        <a class="btn btn-default" @click="!pagination_data.next_page_url? '' : ajaxRequest(pagination_data.url, {page: pagination_data.last_page, per_page: ajax_params.per_page})"
                :disabled="!pagination_data.next_page_url">
          Ultima
        </a>
      </li>
    </ul>

  </nav>

  <select v-model="ajax_params.per_page" v-on:change="ajaxRequest(url, ajax_params)">
    <option v-for="number in pagination_numbers" value="{{number}}">{{number}}</option>
    <option value="{{pagination_data.total}}">Tudo</option>
  </select>

  <label for="search">Filtar</label>
  <input type="text" name="search" v-model="search_param">
  <table class="table table-hover">
    <thead>
      <tr>
        <th style="cursor: pointer" v-for="header in columns" @click="sortKey($key)">
          {{header}}
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="record in response_data | filterBy search_param | orderBy sortByKey sortOrder">
        <td v-for="item in columns">{{record[$key]}}</td>
      </tr>
    </tbody>
  </table>

</template>
