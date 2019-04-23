<template>
	<div class="result">
		<div class="row" :key= "trip.id" v-for="trip in results.availableTrips">
			<div class="col-xs-3">{{trip.busType}}</div>
			<div class="col-xs-3">{{trip.travels}} </div>
			<div class="col-xs-3">{{trip.departureTime}} </div>
			<div class="col-xs-3">{{trip.arrivalTime}}</div>
		</div>
	</div>
</template>
<script>
import axios from 'axios'
export default {
  name: 'result',
  components: {
    
  },mounted() {
		var self = this;
		axios.get(`https://test.railyatri.in/redbus/get-available-trips.json?ry_smart_bus=true&source=${self.$route.query.from_code}&destination=${self.$route.query.to_code}&doj=${self.$route.query.doj}`).then(function(res){
			self.results = res.data;
		}.bind(self)).catch(function (error) {
    // handle error
      console.log(error);
    });
	},data() {
		return {
			results: [],
			bp:{},
			dp:{},
			trip:{},
		}
	}
};
</script>