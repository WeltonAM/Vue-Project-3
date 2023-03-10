<template>
  <div class="user-admin">

    <b-form>
      <input id="user-id" type="hidden" v-model="user.id">

      <b-row>

        <b-col md="6" sm="12">
          <b-form-group label="Nome:" label-for="user-name">
            <b-form-input 
              id="user-name" 
              type="text" 
              required
              placeholder="Informe o Nome do Usuário..."
              v-model="user.name" 
              :readonly="mode === 'remove'" 
            />
          </b-form-group>
        </b-col>

        <b-col md="6" sm="12">
          <b-form-group label="Email:" label-for="user-email">
            <b-form-input 
            id="user-email" 
            type="email" 
            v-model="user.email"
            required
            placeholder="Informe o Email do Usuário"
            :readonly="mode === 'remove'" />
          </b-form-group>
        </b-col>

      </b-row>

      <b-form-checkbox id="user-admin" v-model="user.admin" class="mt-3 mb-3"
        v-show="mode === 'save'">Administrador?</b-form-checkbox>

      <b-row v-show="mode === 'save'">

        <b-col md="6" sm="12">
          <b-form-group label="Senha:" label-for="user-password">
            <b-form-input id="user-password" type="password" v-model="user.password" required
              placeholder="Informe a Senha do Usuário" />
          </b-form-group>
        </b-col>

        <b-col md="6" sm="12">
          <b-form-group label="Confirmação de Senha:" label-for="user-confirmPassword">
            <b-form-input id="user-confirmPassword" type="password" v-model="user.confirmPassword" required
              placeholder="Confirme a Senha do Usuário" />
          </b-form-group>
        </b-col>

      </b-row>

      <b-row>
        <b-col xs="12">
          <b-button variant="primary" v-if="mode === 'save'" @click="save" size="sm">Salvar</b-button>
          <b-button variant="danger" v-if="mode === 'remove'" @click="remove" size="sm">Excluir</b-button>
          <b-button class="ml-2" @click="reset" size="sm">Cancelar</b-button>
        </b-col>
      </b-row>

    </b-form>

    <hr>

    <b-table hover striped :items="users" :fields="fields">
      <template slot="actions" slot-scope="data">

        <b-button variant="warning" @click="loadUser(data.item)" class="mr-2" size="sm">
          <i class="fa fa-pencil"></i>
        </b-button>

        <b-button variant="danger" @click="loadUser(data.item, 'remove')" size="sm">
          <i class="fa fa-trash"></i>
        </b-button>

      </template>
    </b-table>

    <b-pagination size="md" v-model="page" :total-rows="count" :per-page="limit" />

  </div>
</template>

<script>
import { baseApiUrl, showError } from '../../global'
import axios from 'axios'

export default {
  name: 'UserAdmin',
  data: function () {
    return {
      mode: 'save',
      user: {},
      users: [],
      page: 1,
      limit: 0,
      count: 0,
      fields: [
        { key: 'id', label: 'Código', sortable: true },
        { key: 'name', label: 'Nome', sortable: true },
        { key: 'email', label: 'Email', sortable: true },
        { key: 'admin', label: 'Admin', sortable: true, formatter: value => value ? 'Sim' : 'Não' },
        { key: 'actions', label: 'Ações' },
      ]
    }
  },
  methods: {
    loadUsers() {
      const url = `${baseApiUrl}/users?page=${this.page}`
      axios.get(url).then(res => {
        this.users = res.data.data
        this.count = res.data.count 
        this.limit = res.data.limit 
      })
    },
    reset() {
      this.mode = 'save'
      this.user = []
      this.loadUsers()
    },
    save() {
      const method = this.user.id ? 'put' : 'post'
      const id = this.user.id ? `/${this.user.id}` : ''
      axios[method](`${baseApiUrl}/users${id}`, this.user)
        .then(() => {
          this.$toasted.global.defaultSuccess()
          this.reset()
        })
        .catch(showError)
    },
    remove() {
      const id = this.user.id
      axios.delete(`${baseApiUrl}/users/${id}`)
        .then(() => {
          this.$toasted.global.defaultSuccess()
          this.reset()
        })
        .catch(showError)
    },
    loadUser(user, mode = 'save') {
      this.mode = mode
      this.user = { ...user }
    }
  },
  watch: {
    page() {
      this.loadUsers()
    }
  },
  mounted() {
    this.loadUsers()
  }
}
</script>

<style>

</style>