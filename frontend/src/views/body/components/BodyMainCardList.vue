<template>
  <div v-if="!this.$store.state.rooms.isSearch" class="bodygame-card-list py-3  grid grid-cols-2 gap-6">
    <div v-for="room in this.$store.state.rooms.rooms"  :key="room.roomSequence" >
      <BodyMainCardListItem v-if="room.mode.modeSequence===3" :mode="room.mode.modeName" :room="room" />  
    </div>
  </div>
  <div v-if="this.$store.state.rooms.isSearch" class="bodygame-card-list py-3  grid grid-cols-2 gap-6">
    <div v-for="room in this.$store.state.rooms.searchRooms"  :key="room.roomSequence">
      <BodyMainCardListItem v-if="room.mode.modeSequence===3" :mode="room.mode.modeName" :room="room" />  
    </div>
  </div>

  <nav aria-label="Page navigation">
                <ul class="pagination">
                  <li class="page-item">
                    <a class="page-link" href="#" aria-label="Previous" @click="chkRange(this.$store.state.rooms.currentPage-1)">
                      <span aria-hidden="true">&laquo;</span>
                    </a>
                  </li>
                 <ul v-for="idx in this.$store.state.rooms.range" :key="idx">
                    <li class="page-item"><a class="page-link" @click="getSession({page : idx, mode : 'body-game'})" href="#">{{idx+1}}</a></li>
                  </ul>
                  <li class="page-item">
                    <a class="page-link" href="#" aria-label="Next" @click="chkRange(this.$store.state.rooms.currentPage+1)">
                      <span aria-hidden="true">&raquo;</span>
                    </a>
                  </li>
                </ul>
              </nav>

  

</template>

<script>
import BodyMainCardListItem from './BodyMainCardListItem.vue';
import { mapActions } from 'vuex';


export default {
  data () {
    return {
     
    }
  },
  components : {
    BodyMainCardListItem,

  },
  methods : {
     ...mapActions(['chkRange', 'getSession']),
            
  

  },
  created(){
    this.getSession({page: 0, mode : 'body-game'})
    this.$store.state.rooms.isSearch = false

  },
}  

</script>

<style scoped>
.bodygame-card-list{
  display : flex;
  flex-flow: row wrap; 
  width : 100%;
  height : auto;

}
.pagination {
  margin-top: 4%;
  display:flex;
  justify-content: center;
  align-items: center;
  align-self: center;
  
}
</style>