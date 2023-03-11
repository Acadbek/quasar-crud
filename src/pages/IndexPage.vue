<template>
  <q-page padding>
    <q-table title="Products" :rows="posts" :columns="columns" row-key="name">
      <template v-slot:top>
        <span class="text-h5">Products</span>
        <q-space />
        <q-btn
          color="primary"
          label="Create new Product"
          :to="{ name: 'formPost' }"
        />
      </template>
      <template v-slot:body-cell-actions="props">
        <q-td :props="props">
          {{ props.row.name_uz }}
        </q-td>
        <q-td :props="props" class="q-gutter-sm">
          <q-btn
            icon="edit"
            color="info"
            dense
            size="sm"
            @click="handleEditPost(props.row.id)"
          />
          <q-btn
            icon="delete"
            color="negative"
            dense
            size="sm"
            @click="handleDeletePost(props.row.id)"
          />
        </q-td>
      </template>
    </q-table>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter } from 'vue-router'

export default defineComponent({
  name: 'IndexPage',
  setup () {
    const posts = ref([])
    const { list, remove } = postsService()
    const columns = [
      { name: 'id', field: 'id', label: 'Id', sortable: true, align: 'left' },
      {
        name: '',
        field: 'title',
        label: 'Product Name',
        sortable: true,
        align: 'left'
      },
      {
        name: 'Address',
        field: 'Address',
        label: 'Address',
        sortable: true,
        align: 'left'
      },
      { name: 'actions', field: 'actions', label: 'Actions', align: 'right' }
    ]
    const $q = useQuasar()
    const router = useRouter()

    onMounted(() => {
      getPosts()
    })

    const getPosts = async () => {
      try {
        const data = await list()
        posts.value = data
      } catch (error) {
        console.error(error)
      }
    }

    const handleDeletePost = async (id) => {
      console.log(id, 'id')
      try {
        $q.dialog({
          title: 'Delete',
          message: 'Are you shure?',
          cancel: true,
          persistent: true
        }).onOk(async () => {
          await remove(id)
          $q.notify({
            message: 'Successfully deleted',
            icon: 'check',
            color: 'positive'
          })
          await getPosts()
        })
      } catch (error) {
        $q.notify({
          message: 'Error',
          icon: 'times',
          color: 'negative'
        })
      }
    }

    const handleEditPost = (id) => {
      router.push({ name: 'formPost', params: { id } })
    }

    return {
      posts,
      columns,
      handleDeletePost,
      handleEditPost
    }
  }
})
</script>
