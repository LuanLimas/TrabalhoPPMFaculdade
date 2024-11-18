<template>
  <q-page padding class="user-background">
    <q-form>
      <!-- q-mb-md significa que a margem inferior será de 24px -->
      <div class="q-mb-md">
        <q-input v-model="name" label="Nome" outlined/>
      </div>
      <!-- row significa que os elementos serão alinhados horizontalmente -->
      <div class="row q-col-gutter-x-md">
        <q-input class="col-8" mask="##/##/####" v-model="bornDate" label="Data Nascimento" outlined>
          <template v-slot:append>
            <q-icon name="event" class="cursor-pointer">
              <q-popup-proxy>
                <q-date v-model="bornDate" mask="DD/MM/YYYY" />
              </q-popup-proxy>
            </q-icon>
          </template>
        </q-input>
        <q-select class="col-4" v-model="gender"
                  label="Genero"
                  outlined
                  :options="[
                  'Masculino',
                  'Feminino',
                  'Outro'
                ]"
        />
      </div>
      <password-input class="q-mt-md" outlined label="Senha" v-model="password" @change-icon="onChangeIcon">
        <template v-slot:icon>
          <q-icon name="password" />
        </template>
      </password-input>
      <div class="column">
        <q-checkbox v-model="term" label="Aceito os termos de uso"/>
        <q-btn label="Salvar" color="primary" @click="onSave"/>
        <q-btn label="Listar Usuários" color="secondary" @click="onListUsers" class="q-mt-sm"/>
      </div>
    </q-form>
  </q-page>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue'
import { useQuasar } from 'quasar'

export default defineComponent({
  name: 'UserPage',
  setup () {
    const { notify } = useQuasar()

    const name = ref<string>('')
    const bornDate = ref<string>('')
    const gender = ref<string>('')
    const term = ref<boolean>(false)
    const password = ref<string>('')

    const users = ref<Array<{ name: string, bornDate: string, gender: string, password: string }>>([])

    const onSave = () => {
      // Verifica se todos os campos estão preenchidos
      if (name.value && bornDate.value && gender.value && password.value && term.value) {
        const user = {
          name: name.value,
          bornDate: bornDate.value,
          gender: gender.value,
          password: password.value
        }

        // Adiciona o usuário à lista de usuários
        users.value.push(user)

        // Notificação de sucesso
        notify({
          message: `${name.value} salvo com sucesso`,
          type: 'positive'
        })

        // Limpa os campos após salvar
        name.value = ''
        bornDate.value = ''
        gender.value = ''
        password.value = ''
        term.value = false
      } else {
        // Notifica se algum campo não foi preenchido
        notify({
          message: 'Por favor, preencha todos os campos.',
          type: 'negative'
        })
      }
    }

    const onListUsers = () => {
      if (users.value.length === 0) {
        notify({
          message: 'Nenhum usuário salvo.',
          type: 'info'
        })
      } else {
        // Exibe os usuários salvos
        const userList = users.value.map(user => `${user.name} - ${user.bornDate} - ${user.gender}`).join('\n')
        notify({
          message: `Usuários salvos:\n${userList}`,
          type: 'info'
        })
      }
    }

    const onChangeIcon = (value: boolean) => {
      if (value) {
        notify({
          message: 'Ocultando senha',
          type: 'info'
        })
      } else {
        notify({
          message: 'Mostrando senha',
          type: 'info'
        })
      }
    }

    return {
      name,
      bornDate,
      gender,
      term,
      password,
      onSave,
      onListUsers,
      onChangeIcon
    }
  }
})
</script>
<style lang="scss" scoped>
.user-background {
  background-color: white;
}
</style>
