<template>
  <div class="books">
    <div class="button">
      <a-button type="primary" @click="showModal">
        + Добавить книгу
      </a-button>
    </div>
    <a-table :columns="columns" :data-source="rows" v-if="rows">
      <span slot="name">Название книги</span>
      <span slot="author">Автор</span>
      <span slot="genreSet" slot-scope="genreSet">
        <a-tag
          v-for="genre in genreSet"
          :key="genre.id"
          :color="genre === 'Повесть' ? 'volcano' : genre.length > 5 ? 'geekblue' : 'green'"
        >
          {{ genre.name }}
        </a-tag>
      </span>
      <span slot="action" slot-scope="text, record">
        <a>Комментарии</a>
        <a-divider type="vertical" />
        <a>Редактировать</a>
        <a-divider type="vertical" />
        <a>Удалить</a>
      </span>
    </a-table>
    <a-empty v-else />
    <book-form
      ref="bookForm"
      :visible="visible"
      :authors="authors"
      :genres="genres"
      @cancel="handleCancel"
      @create="handleCreate"
    />
  </div>
</template>

<script>
  import axios from "axios";
  import BookForm from '@/components/BookForm'

  const columns = [
    {
      dataIndex: 'name',
      key: 'name',
      slots: { title: 'name' },
    },
    {
      dataIndex: 'author.name',
      key: 'author',
      slots: { title: 'author' },
    },
    {
      title: 'Жанры',
      key: 'genreSet',
      dataIndex: 'genreSet',
      slots: { title: 'genreSet' },
      scopedSlots: { customRender: 'genreSet' },
    },
    {
      title: 'Действие',
      key: 'action',
      scopedSlots: { customRender: 'action' },
    },
  ];

  // const authors = [{id : 1, name: "21"}, {id: 2, name:"123"}]

  export default {
        name: "BookList",
        components: {BookForm},
        data() {
          return {
            rows : [],
            columns,
            visible: false,
            authors : [],
            genres: []
          };
        },
        beforeMount() {
          const that = this;

          /* Загружаем книги */
          this.loadBooks();

          /* Список авторов */
          axios
            .get('/v1/author')
            .then(response =>  that.authors = response.data)

          /* Список жанров */
          axios
            .get('/v1/genre')
            .then(response =>  that.genres = response.data)
        },
        methods: {
          loadBooks() {
            const that = this
            axios
              .get('/v1/book')
              .then(response => {
                console.log(response)
                response.data.forEach(item => item.key = item.id)
                that.rows = response.data
              })
          },
          showModal() {
            this.visible = true;
          },
          handleCancel() {
            this.visible = false;
          },
          handleCreate() {
            const form = this.$refs.bookForm.form;
            form.validateFields((err, values) => {
              if (err) {
                return;
              }
              console.log('Received values of form: ', values);
              const postData = {name: values.name, author: {id : values.author}, genreSet : values.genres.map(g => {return {id: g}})}

              axios
                .post('/v1/book', postData)
                .then(() =>  {
                  this.loadBooks();
                })
                .catch((err) =>  {
                  alert("Ошибка при добавлении книги")
                  console.log(err)
                })
              form.resetFields();
              this.visible = false;
            });
          },
        },
  }
</script>

<style scoped>
  .button {
    text-align: left;
    margin: 1rem;
  }
</style>
