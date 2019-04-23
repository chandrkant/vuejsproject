<template>
	<div class="search">
		<h1>Search page</h1>
		<div class="col-xs-7"> 
			<div class="col-lg-6">
        <Autocomplete :items="source" inputId="from-code" filterby="name" @change="onChange" title="From City" @selected="fromCitySelected" ></Autocomplete>
        <input type="hidden" v-model="formCode">
      </div>
      <div class="col-lg-6">
        <Autocomplete :items="dest" filterby="name" inputId="to-code" @change="onChange" title="To City" @selected="toCitySelected" ></Autocomplete>
        <input type="hidden" v-model="toCode">
      </div>
		</div>
    <div class="col-xs-3">
      <datepicker id="datepicker" 
      input-class="my-datepicker form-control" 
      wrapper-class="my-datepicker-div" 
      format="dd-MM-yyyy" 
      placeholder="Please select Date .."
      @selected = "dateSelected"
      :value="new Date()"
      :disabled-dates="disabledDates"
      ></datepicker>
    </div>
		<div class="col-xs-2"> 
			<button class="btn btn-primary" @click = "getTrips">Get List</button>
		</div>
	</div>
</template>
<script>
// @ is an alias to /src
import axios from 'axios'
import Datepicker from 'vuejs-datepicker';
import Autocomplete from '@/components/Autocomplete.vue'
export default {
  name: 'search',
  data(){
    return {
      source: [],
      dest: [],
      cityList: [],
      formCode: '',
      toCode: '',
      disabledDates:{to: new Date()},
      doj: `${new Date().getDate()}-${new Date().getMonth()+1}-${new Date().getFullYear()}`
    }
  },
  components: {
    Autocomplete: Autocomplete,
    Datepicker:  Datepicker
  },
  mounted() {
  	var self = this;
    axios.get(`https://test.railyatri.in/redbus/get-smart-bus-routes.json`).then(function (response){
    var list = []
		self.cityList = response.data.smart_bus_routes
    list = self.removeDuplicates(self.cityList,'source_city_id')
    list = list.map(city=>({name: city.source_city, code: city.source_city_id}))
    self.source = list
    self.dest = list
  	}).catch(function (error) {
    // handle error
    	console.log(error);
  	})
  },
  methods: {
  	onChange(){

  	},
    getTrips(){
      this.$router.push({ name: 'result', params: { from: this.$children[0].$data.code.replace(' ', '-'),to: this.$children[1].$data.code.replace(' ', '-')},query: {from_code: this.formCode,to_code: this.toCode,doj: this.doj} })  
    },
  	fromCitySelected(city) {
      var templist = []
      var self = this;
      self.formCode = city.code;
      console.log(`City Selected:\nid: ${city.code}\nname: ${city.name}`);
      templist = self.cityList.filter((obj, pos, arr) => {
          return obj.source_city_id=== city.code
      });
      self.dest = templist.map(city=>({name: city.destination_city, code: city.destination_city_id}))
    },
    toCitySelected(city) {
      this.toCode = city.code;
      console.log(`City Selected:\nid: ${city.code}\nname: ${city.name}`);
    },
    removeDuplicates(myArr, prop) {
      return myArr.filter((obj, pos, arr) => {
          return arr.map(mapObj => mapObj[prop]).indexOf(obj[prop]) === pos;
      });
    },dateSelected(date){
        this.doj = `${date.getDate()}-${date.getMonth()+1}-${date.getFullYear()}`
    }
  }
};
</script>
<style type="text/css" media="screen">	
 .col-mid {
 	  width: 45%;
    margin: auto;
    position: relative;
    float: left;
    margin-left: 15px;
    margin-right: 15px;
 }
 .btn-cls{
    padding: 15px;
 }
 .submit-button{
    border: 1px solid #d5d6d9;
    padding: 10px;
    border-radius: 4px;
    background: #0b75d3;
    color: #FFF;
    font-weight: 600;
    width: 50%;
    margin: 0 auto;
 }
</style>
