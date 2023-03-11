<template>
  <q-page padding>
    <q-form @submit.prevent="onSubmit" class="row q-col-gutter-sm">
      <q-input
        outlined
        v-model="form.name_uz"
        label="Product Name"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[(val) => (val && val.length > 0) || 'Error']"
      />

      <q-input
        outlined
        v-model="form.cost"
        label="Cost"
        type="number"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[(val) => (val && val.length > 0) || 'Error']"
      />

      <q-input
        outlined
        v-model="form.address"
        label="Address"
        lazy-rules
        class="col-lg-6 col-xs-12"
        :rules="[(val) => (val && val.length > 0) || 'Error']"
      />

      <q-input
        outlined
        v-model="form.created_date"
        label=""
        lazy-rules
        type="date"
        class="col-lg-6 col-xs-12"
        :rules="[(val) => (val && val.length > 0) || 'Error']"
      />

      <div class="col-lg-12 col-xs-12">
        <q-editor v-model="form.product_type_id" min-height="5rem" />
      </div>

      <div class="col-12 q-gutter-sm">
        <q-btn
          label="Salvar"
          color="primary"
          class="float-right"
          icon="save"
          type="submit"
        />
        <q-btn
          label="Cancelar"
          color="white"
          class="float-right"
          text-color="primary"
          :to="{ name: 'home' }"
        />
      </div>
    </q-form>
  </q-page>
</template>

<script>
import { defineComponent, ref, onMounted } from 'vue'
import postsService from 'src/services/posts'
import { useQuasar } from 'quasar'
import { useRouter, useRoute } from 'vue-router'

export default defineComponent({
  name: 'FormPost',
  setup () {
    const { post, getById, update } = postsService()
    const $q = useQuasar()
    const router = useRouter()
    const route = useRoute()
    const form = ref({
      product_type_id: 0,
      name_uz: '',
      cost: 0,
      address: '',
      created_date: ''
    })

    onMounted(async () => {
      if (route.params.id) {
        getPost(route.params.id)
      }
    })

    const getPost = async (id) => {
      try {
        const response = await getById(id)
        form.value = response
      } catch (error) {
        console.error(error)
      }
    }

    const onSubmit = async () => {
      try {
        if (form.value.id) {
          await update(form.value)
          console.log('post')
        } else {
          await post(form.value)
          console.log('update')
        }
        $q.notify({
          message: 'Post salvo com sucesso!',
          icon: 'check',
          color: 'positive'
        })
        router.push({ name: 'home' })
      } catch (error) {
        console.error(error)
      }
    }

    return {
      form,
      onSubmit
    }
  }
})
</script>
