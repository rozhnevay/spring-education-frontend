<template>
  <a-modal
    :visible="visible"
    title='Добавить книгу'
    okText='Добавить'
    cancelText='Отмена'
    @cancel="() => { $emit('cancel') }"
    @ok="() => { $emit('create') }"
  >
    <a-form layout='vertical' :form="form">
      <a-form-item label='Название'>
        <a-input
          v-decorator="[
              'name',
              {
                rules: [{ required: true, message: 'Пожалуйста, введите название книги!' }],
              }
            ]"
        />
      </a-form-item>
      <a-form-item label='Автор'>
        <a-select
          v-decorator="[
          'author',
          { rules: [{ required: true, message: 'Пожалуйста, выберите автора!' }] },
        ]"
          placeholder="Пожалуйста, выберите автора"
        >
          <a-select-option v-for="author in authors" :key="author.id" :value="author.id" :data-value="author">
            {{author.name}}
          </a-select-option>
        </a-select>
      </a-form-item>
      <a-form-item label='Жанры'>
        <a-select
          v-decorator="[
          'genres',
          { rules: [{ required: true, message: 'Пожалуйста, выберите жанры!' }] },
        ]"
          mode="multiple"
          placeholder="Пожалуйста, выберите жанры"
        >
          <a-select-option v-for="genre in genres" :key="genre.id" :value="genre.id">
            {{genre.name}}
          </a-select-option>
        </a-select>
      </a-form-item>
    </a-form>
  </a-modal>
</template>

<script>
    export default {
        name: "BookForm",
        props: ['visible', 'authors', 'genres'],
        beforeCreate() {
          this.form = this.$form.createForm(this, { name: 'form_in_modal' });
        }
    }
</script>

<style scoped>

</style>
