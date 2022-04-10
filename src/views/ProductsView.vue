<template>
  <div class="text-end">
    <button class="btn btn-primary" type="button" @click="openModal(true)">
      新增產品
    </button>
  </div>
  <table class="table mt-4">
    <thead>
      <tr>
        <th width="120">分類</th>
        <th>產品名稱</th>
        <th width="120">原價</th>
        <th width="120">售價</th>
        <th width="100">是否啟用</th>
        <th width="200">編輯</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in products" :key="item.id">
        <td>{{ item.category }}</td>
        <!--分類-->
        <td>{{ item.title }}</td>
        <!--標題-->
        <td class="text-right">{{ item.origin_price }}</td>
        <td class="text-right">{{ item.price }}</td>
        <td>
          <span class="text-success" v-if="item.is_enabled">啟用</span>
          <span class="text-success" v-else>未啟用</span>
        </td>
        <td>
          <div class="btn-group">
            <button
              class="btn btn-outline-primary btn-sm"
              @click="openModal(false, item)"
            >
              編輯
            </button>
            <button class="btn btn-outline-danger btn-sm">刪除</button>
          </div>
        </td>
      </tr>
    </tbody>
  </table>
  <ProductModal
    ref="ProductModal"
    :product="tempProduct"
    @update-product="updateProduct"
  ></ProductModal>
</template>

<script>
import ProductModal from '../components/ProductModal-component.vue';
export default {
  data () {
    return {
      products: [],
      pagination: {},
      tempProduct: {},
      is_New: false
    };
  },
  components: {
    ProductModal
  },
  methods: {
    openModal (isNew, item) {
      const productComponent = this.$refs.ProductModal;
      if (isNew) {
        this.tempProduct = {};
        this.is_New = true;
      } else {
        this.tempProduct = { ...item };
        this.is_New = false;
      }
      productComponent.showModal();
    },
    getProducts () {
      const api = `${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/admin/products`;
      console.log(api);
      this.$http.get(api).then((response) => {
        console.log(response);
        this.products = response.data.products;
      });
    },
    updateProduct (item) {
      console.log('item=', item);
      this.tempProduct = item;
      // 新增
      const productComponent = this.$refs.ProductModal;
      let api = `${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/admin/product`;
      let httpMethod = 'post';
      // 編輯
      if (!this.is_New) {
        api = `${process.env.VUE_APP_API}/api/${process.env.VUE_APP_PATH}/admin/product/${item.id}`;
        httpMethod = 'put';
      }

      this.$http[httpMethod](api, { data: this.tempProduct }).then((response) => {
        console.log(response);
        productComponent.hideModal();
        this.getProducts();
      });
    }
  },
  created () {
    this.getProducts();
  }
};
</script>
