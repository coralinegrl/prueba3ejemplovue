<template>
  <ion-content>
    <div class="container">
      <ion-list>
        <ion-item v-for="product in products" :key="product.id">
          <ion-thumbnail slot="start">
            <img :src="product.image" alt="Product Image" />
          </ion-thumbnail>
          <ion-label>
            <h2 class="product-title">{{ product.name }}</h2>
            <p class="product-description">{{ product.description }}</p>
            <p class="product-price">Price: {{ product.price }}</p>
            <p class="product-quantity">Quantity: {{ product.quantity }}</p>
            <p class="product-status">Status: {{ product.status }}</p>
          </ion-label>
          <ion-button color="primary" @click="handleEdit(product)" class="edit-button">Edit</ion-button>
          <ion-button color="danger" @click="handleDelete(product.id)" class="delete-button">Delete</ion-button>
        </ion-item>
      </ion-list>

      <ion-card v-if="editProduct !== null" class="edit-card">
        <ion-card-header>
          <ion-card-title>Edit Product</ion-card-title>
        </ion-card-header>
        <ion-card-content>
          <ion-input v-model="editProduct.name" placeholder="Name" class="edit-input"></ion-input>
          <ion-input v-model="editProduct.description" placeholder="Description" class="edit-input"></ion-input>
          <ion-input v-model="editProduct.price" placeholder="Price" type="number" class="edit-input"></ion-input>
          <ion-input v-model="editProduct.quantity" placeholder="Quantity" type="number" class="edit-input"></ion-input>
          <ion-input v-model="editProduct.status" placeholder="Status" class="edit-input"></ion-input>
          <ion-button color="primary" @click="handleSave" class="save-button">Save</ion-button>
        </ion-card-content>
      </ion-card>
    </div>
  </ion-content>
</template>

<script>
import { IonContent, IonList, IonItem, IonThumbnail, IonLabel, IonButton, IonCard, IonCardHeader, IonCardTitle, IonCardContent, IonInput } from '@ionic/vue';
export default {
  data() {
    return {
      products: [],
      editProduct: null,
    };
  },
  components: {
    IonContent,
    IonList,
    IonItem,
    IonThumbnail,
    IonLabel,
    IonButton,
    IonCard,
    IonCardHeader,
    IonCardTitle,
    IonCardContent,
    IonInput,
  },
  mounted() {
    // Replace with your API endpoints
    fetch('http://localhost:8000/api/products')
      .then((response) => response.json())
      .then((data) => {
        this.products = data;
      });
  },
  methods: {
    handleEdit(product) {
      this.editProduct = { 
        id: 1,
        name: product.name,
        description: product.description,
        price: product.price,
        quantity: product.quantity,
        status: product.status,
       };
      console.log(this.editProduct);
    },
    handleSave() {
      if (!this.editProduct.id) {
        alert('Selecciona un producto para editar.');
        return;
      }
      fetch(`http://localhost:8000/api/products/${this.editProduct.id}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(this.editProduct),
      })
      .then(response => {
        if (!response.ok) {
          throw new Error('Error al guardar');
        }
        return response.json();
      })
      .then(() => {
        this.fetchProducts(); // Recargar la lista de productos
        this.editProduct = null; // Resetear el producto en edición
      })
      .catch(error => {
        console.error('Error:', error);
        alert('Error al guardar los cambios. Intente de nuevo.');
      });
    },
    handleDelete(id) {
      // Replace with your API endpoint
      fetch(`http://localhost:8000/api/products/${id}`, {
        method: 'DELETE',
      })
        .then(() => {
          this.products = this.products.filter((product) => product.id !== id);
        });
    },
  },
};
</script>

<style scoped>
.container {
  padding: 20px;
  background-color: #f2f2f2;
}

.product-title {
  font-size: 20px;
  font-weight: bold;
}

.product-description {
  color: #444;
}

.product-price,
.product-quantity,
.product-status {
  margin-top: 5px;
  color: #666;
}

.edit-card {
  margin-top: 20px;
  background-color: #fff;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.edit-input {
  margin-bottom: 10px;
}

.save-button {
  margin-top: 10px;
}
</style>
