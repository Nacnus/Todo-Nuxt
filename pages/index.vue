<template>
  <v-container>

    <v-card v-for="(item,i) in list" :key="i" class="d-flex ma-1 align-center px-2">

      <v-checkbox
        color="orange"
        v-model="item.is_checked"
        @change="updateTodo(item)"
      />

      <v-text-field
        v-model="item.text"
        :disabled="item.isEdit"
      />

      <v-btn @click="updateTodo(item)" icon small>
        <v-icon>
          mdi-{{ !item.isEdit ? 'content-save' : 'pencil' }}
        </v-icon>
      </v-btn>

      <v-btn
        icon
        small
        @click="deleteTodo(item.id)"
      >
        <v-icon>mdi-delete-forever-outline</v-icon>
      </v-btn>

    </v-card>

    <v-text-field
      label="Ekleme"
      v-model="newText"
      @keydown.enter="addTodo(newText)"
      append-icon="mdi-plus"
      @click:append="addTodo(newText)"
    />
  </v-container>
</template>

<script>
export default {
  data(){
    return{
      list: [],
      newText: '',
      textDis: true
    }
  },
  created() {
    this.getTodo()
  },
  methods:{
    getTodo(){
      this.$axios.$get('todo/')
        .then((response) => {
          this.list = response.data.map((e) => {
            e.isEdit = true
            return e
          })
        })
    },
    deleteTodo(item){
      this.$axios.$delete(`todo/${item}`)
      this.list = this.list.filter(e => e.id !== item)
    },
    updateTodo(item) {
      if (!item.isEdit){
        item.isEdit = true
        return
      }
      const payload = {
        text: item.text,
        is_checked: item.is_checked
      }
      this.$axios.$patch(`todo/${item.id}/`, payload)
        .then((response) => {
          this.list.find(e => e.id === response.id).text = response.text
          item.isEdit = false
        })
    },
    addTodo(item){
      const payload = {
        text:item,
        is_checked:false
      }
      this.$axios.$post('todo/',payload)
        .then((response) => {
          this.list.push(response)
        })
      this.newText=''
    }
  }
}
</script>
<style>

</style>
