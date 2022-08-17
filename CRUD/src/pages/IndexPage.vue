<template>
  <q-page padding>
  <q-table
      title="Treats"
      :rows="posts"
      :columns="columns"
      row-key="name"
    >

    <template v-slot:top>
      <span class="text-h5">Artigos</span>
      <q-space />
      <q-btn color="primary" label="Novo" :to="{ name: 'formPost' }" />
    </template>
    <template v-slot:body-cell-actions="props">
      <q-td :props="props" class="q-gutter-sm" >
        <q-btn icon="edit" color="info" dense size="sim" @click="handleEditPost(props.row.id)" />
        <q-btn icon="delete" color="negative" dense size="sim" @click="handleDelete(props.row.id)" />

      </q-td>
    </template>
  </q-table>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsServices from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter } from 'vue-router'

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const posts = ref([])
    const { list, remove } = postsServices()
    const columns = [
      { name: 'id', field: 'id', label: 'id', sortable: true, align: 'left' },
      { name: 'title', field: 'title', label: 'title', sortable: true, align: 'left' },
      { name: 'author', field: 'author', label: 'author', sortable: true, align: 'left' },
      { name: 'actions', field: 'actions', label: 'actions', align: 'left' }
    ]
    const $q = useQuasar()
    const router = useRouter()

    onMounted(() => { getposts() })

    const getposts = async () => {
      try {
        const data = await list()
        posts.value = data
      } catch (erro) {
        console.log(erro)
      }
    }

    const handleDelete = async (id) => {
      try {
        $q.dialog({
          title: 'Remover',
          message: 'Deseja remover esta postagem ?',
          cancel: true,
          persistent: true
        }).onOk(async () => {
          await remove(id)
          $q.notify({ message: 'Apagado com sucesso', icon: 'check', color: 'positive' })
          await getposts()
        })
      } catch (erro) {
        $q.notify({ message: 'Erro ao apagar post', icon: 'times', color: 'negative' })
      }
    }

    const handleEditPost = async (id) => {
      router.push({ name: 'formPost', params: { id } })
    }

    return {
      posts,
      columns,
      handleDelete,
      handleEditPost
    }
  }
})
</script>
