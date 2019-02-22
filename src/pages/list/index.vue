<template>
  <div class="container">
    <div class="list">
      <navigator 
        v-for="item in list"
        :key="item.id"
        :url="'../item/main?id='+item.id"
      >
        <product :item="item"></product>
      </navigator>
      
    </div>
  </div>

</template>

<script>
import douban from '../../utils/douban.js'
import product from '@/components/product.vue';
export default {
  data () {
    return {
      title: '?????',//???????????
      type: '',
      page: 1, //??
      size: 10,//??
      list: [
        // {id:'', image: '', title: '', average: 4.3, original_title: '', year: '', directors:''}
      ]
    }
  },

  components: {
    product
  },

  methods: {
    loadList: function () {
      douban({
        url: '/v2/movie/' + this.type,
        data: {
          count: this.size,
          start: this.page++
        },
        loading: true
      }).then(
        res => {
          if (!res.data.subjects) return;
          let result = [];
          res.data.subjects.map((item) => {
            result.push({
              image: item.images.small,
              id: item.id,
              title: item.title,
              average: item.rating.average,
              original_title: item.original_title,
              year: item.year,
              directors: item.directors[0].name
            })
          });
          this.list=result;
          this.title=res.data.title;
        }
      )
    }
  },

  
  beforeMount: function () {
    console.log(this.$root.$mp.query)
    this.title = this.$root.$mp.query.title || this.title

    //  in_theaters  coming_soon  us_box
    this.type =this.$root.$mp.query.key || this.type
    this.loadList();
  },
  mounted: function () {
    mpvue.setNavigationBarTitle({
      title: this.title + '电影》豆瓣'
    });
  }
}
</script>

<style scoped>
.container {
  display: flex;
  flex: 1;
  flex-direction: column;
  box-sizing: border-box;
}

.list {
  display: flex;
  flex: 1;
  flex-direction: column;
}


</style>
