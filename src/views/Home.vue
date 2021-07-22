<template>
    <div class="flex flex-col h-screen max-h-screen">
        <div class="flex justify-center bg-hero-pattern bg-cover relative px-4 pb-32">
           <div class="w-full max-w-screen-sm">
             <h1 class="text-white text-center text-3xl pb-4">
               Address
             </h1>
             <div class="flex">
               <input v-model="queryIp" type="text" 
               class="flex-1 py-3 px-2 rounded-tl-md  rounded-bl-md focus:outline-none"
               placeholder="Search address..."
               >
               <i 
               @click="getIpInfo"
               class="cursor-pointer
                bg-black
                 text-white
                  px-4 
                  rounded-tr-md
                  rounded-br-md
                  fas
                  flex
                  items-center
                  fa-chevron-right"></i>
             </div>
           </div>
           <Ipinfo v-if="IpInfo" :ipInfo="IpInfo"/>
        </div>
        <div id="mapid" class="h-full z-10">

        </div>
    </div>

</template>

<script>
import Ipinfo from '../components/Ipinfo.vue'
import leaflet from 'leaflet'
import { onMounted, ref } from 'vue'
import axios from 'axios'

export default {
  name: 'Home',
  components: {
    Ipinfo
  },
  setup() {
    let mymap

    const queryIp = ref("")
    const IpInfo = ref(null)
    onMounted(() => {

      mymap = leaflet.map('mapid').setView([51.505, -0.09], 13);
      leaflet.tileLayer("https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2txcGJ3bmoyMHRjMzJ2cGNpMnlsc3pqeiJ9.mSoT7QxEgjVEmNRN9k_NKg", {
        attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
        maxZoom: 18,
        id: 'mapbox/streets-v11',
        tileSize: 512,
        zoomOffset: -1,
        accessToken: 'pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2txcGJ3bmoyMHRjMzJ2cGNpMnlsc3pqeiJ9.mSoT7QxEgjVEmNRN9k_NKg'
      }).addTo(mymap);
    })
     const getIpInfo = async () => {
        try {
          const data = await axios.get(`https://geo.ipify.org/api/v1?apiKey=at_mtPjbPvlgERwpyseiqOmj3FAGMmJo&ipAddress=${queryIp.value}`)
          const result = data.data
          IpInfo.value = {
                address: result.ip,
                state: result.location.region,
                timezone: result.location.timezone,
                isp: result.isp,
                lat: result.location.lat,
                lng: result.location.lng,
          }
           leaflet.marker([IpInfo.value.lat, IpInfo.value.lng]).addTo(mymap);
           mymap.setView([IpInfo.value.lat, IpInfo.value.lng], 13);
        } catch (error) {
          alert(error.message)
        }
     } 
     return {
       queryIp, IpInfo, getIpInfo
     }
  }
}
</script>
