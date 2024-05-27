<template>
  <div class="container">
    <form class="form" @submit.prevent="save">
      <input type="text" v-model="form.title" placeholder="Title" class="input"/><br />
      <textarea v-model="form.content" placeholder="Content" class="textarea"></textarea><br />
      <button type="submit" class="button">Save</button>
    </form>
    <ul class="article-list">
      <li v-for="article in articles" :key="article.id" class="article-item">
        <strong>{{ article.title }}</strong><br />
        {{ article.content }}<br />
        <button @click="edit(article)" class="button">Edit</button>
        <button @click="deleteArticle(article.id)" class="button">Delete</button>
      </li>
    </ul>
    <button @click="load" class="button load-button">Load</button>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios'; 

export default {
  setup() {
    const form = reactive({
      id: '',
      title: '',
      content: '', 
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get('http://localhost:3000/articles');
        articles.value = response.data;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    }

    async function save() {
      try {
        const url = form.id 
          ? `http://localhost:3000/articles/${form.id}` 
          : 'http://localhost:3000/articles';
        const method = form.id ? 'put' : 'post';
        const response = await axios[method](url, { title: form.title, content: form.content });

        if (form.id) {
          // Update existing article
          articles.value = articles.value.map((article) => 
            article.id === response.data.id ? response.data : article
          );
        } else {
          // Add new article
          articles.value.push(response.data);
        }

        // Reset form
        resetForm();
      } catch (error) {
        console.error('Error saving article:', error);
      }
    }

    function resetForm() {
      form.id = '';
      form.title = '';
      form.content = '';
    }

    async function deleteArticle(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter(article => article.id !== id);
      } catch (error) {
        console.error('Error deleting article:', error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, deleteArticle };
  }
};
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;500;700&display=swap');

.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  font-family: 'Roboto', Arial, sans-serif;
  background: linear-gradient(145deg, #f0f0f0, #fafafa);
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease-in-out;
}

.container:hover {
  transform: translateY(-5px);
}

.form {
  margin-bottom: 20px;
}

.input, .textarea {
  width: 100%;
  padding: 14px;
  margin-bottom: 14px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 16px;
  color: #000;  /* Warna teks hitam */
  transition: border-color 0.3s;
}

.input:focus, .textarea:focus {
  border-color: #007BFF;
  outline: none;
  box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
}

.button {
  padding: 12px 24px;
  background: linear-gradient(145deg, #FF5733, #C70039);  
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-size: 16px;
  font-weight: 500;
  transition: background 0.3s, transform 0.3s;
}

.button:hover {
  background: linear-gradient(145deg, #C70039, #900C3F);
  transform: translateY(-3px);
}

.article-list {
  list-style-type: none;
  padding: 0;
}

.article-item {
  background: white;
  padding: 18px;
  border: 1px solid #ddd;
  border-radius: 8px;
  margin-bottom: 14px;
  transition: box-shadow 0.3s, transform 0.3s;
}

.article-item:hover {
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
  transform: translateY(-5px);
}

.article-item strong {
  display: block;
  font-size: 20px;
  margin-bottom: 8px;
  font-weight: 700;
}

.delete-button {
  background: linear-gradient(145deg, #FF0000, #B22222);  
  border: none;
  color: white;
  padding: 10px 20px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 16px;
  transition: background 0.3s, transform 0.3s;
}

.delete-button:hover {
  background: linear-gradient(145deg, #B22222, #800000);
  transform: translateY(-3px);
}

.load-button {
  display: block;
  width: 100%;
  margin-top: 20px;
  background: linear-gradient(145deg, #32CD32, #228B22);  
  color: white;
  border: none;
  padding: 12px 24px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 16px;
  font-weight: 500;
  transition: background 0.3s, transform 0.3s;
}

.load-button:hover {
  background: linear-gradient(145deg, #228B22, #006400);
  transform: translateY(-3px);
}
</style>
