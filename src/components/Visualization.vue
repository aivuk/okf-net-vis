<script setup>
import { ref, onMounted } from 'vue'
import * as vis from "vis-network/standalone";
import csv from '../assets/okfn-network-experts.csv'
import brazilFlag from "../assets/images/countries/brazil-flag.png"
import argentinaFlag from "../assets/images/countries/argentina-flag.png"
import australiaFlag from "../assets/images/countries/australia-flag.png"
import bangladeshFlag from "../assets/images/countries/bangladesh-flag.png"
import belgiumFlag from "../assets/images/countries/belgium.svg"
import czechRepublicFlag from "../assets/images/countries/czech_republic-flag.png"
import ecuadorFlag from "../assets/images/countries/ecuador-flag.png"
import estoniaFlag from "../assets/images/countries/estonia-flag.png"
import ethiopiaFlag from "../assets/images/countries/ethiopia-flag.png"
import finlandFlag from "../assets/images/countries/finland-flag.svg"
import franceFlag from "../assets/images/countries/france-flag.svg"
import germanyFlag from "../assets/images/countries/germany-flag.webp"
import greeceFlag from "../assets/images/countries/greece-flag.png"
import irelandFlag from "../assets/images/countries/ireland-flag.png"
import italyFlag from "../assets/images/countries/italy-flag.png"
import romaniaFlag from "../assets/images/countries/romania-flag.png"
import spainFlag from "../assets/images/countries/spain-flag.png"
import swedenFlag from "../assets/images/countries/sweden-flag.svg"
import switzerlandFlag from "../assets/images/countries/swiss-flag.png"
import russiaFlag from "../assets/images/countries/russia-flag.svg"
import gambiaFlag from "../assets/images/countries/gambia-flag.png"
import hongKongFlag from "../assets/images/countries/hong_kong-flag.png"
import japanFlag from "../assets/images/countries/japan-flag.png"
import kenyaFlag from "../assets/images/countries/kenya-flag.png"
import nepalFlag from "../assets/images/countries/nepal-flag.png"
import nigeriaFlag from "../assets/images/countries/nigeria-flag.png"
import philippinesFlag from "../assets/images/countries/philippines-flag.png"
import somaliaFlag from "../assets/images/countries/somalia-flag.png"


defineProps({
})

const count = ref(0)

const flags = {
  "Brazil": brazilFlag,
  "Argentina": argentinaFlag,
  "Australia": australiaFlag, 
  "Bangladesh": bangladeshFlag, 
  "Belgium": belgiumFlag,
  "Czech Republic": czechRepublicFlag,
  "Ecuador": ecuadorFlag,
  "Estonia": estoniaFlag,
  "Ethiopia": ethiopiaFlag,
  "Finland": finlandFlag,
  "France": franceFlag,
  "Gambia": gambiaFlag,
  "Germany": germanyFlag,
  "Greece": greeceFlag,
  "Hong Kong": hongKongFlag,
  "Ireland": irelandFlag,
  "Italy": italyFlag,
  "Japan": japanFlag,
  "Kenya": kenyaFlag,
  "Nepal": nepalFlag,
  "Nigeria": nigeriaFlag,
  "Philippines": philippinesFlag,
  "Romania": romaniaFlag,
  "Russian Federation": russiaFlag,
  "Somalia": somaliaFlag,
  "Spain": spainFlag,
  "Sweden": swedenFlag,
  "Switzerland": switzerlandFlag
}

const countries = []
const nodesList = []
const countriesIds = {}
const edges = []

let s = 1;
let c = 1;

for (let specialist of csv) {
  if (specialist.Country.trim() in flags) {
    if (!(specialist.Country.trim() in countriesIds)) {
      countriesIds[specialist.Country.trim()] = c++
    }
    let image = specialist["Pic/Photo"].split('.')[0]
    let node = {
      "id": s,
      "title": specialist.Name,
      "image": `https://network.okfn.org/images/directory/${image}-small.jpg`,
      "shape": "circularImage",
      "size": 30,
      "borderWidth": 3,
      "color": "#FF80DB"
    }
    edges.push({
      "from": s,
      "to": specialist.Country.trim(),
      "width": 4,
      "color": "#FF80DB"
    })
    nodesList.push(node)
    s += 1
  }
}

for (let country in countriesIds) {
  countriesIds[country] += (s - 1)
  let node = {
      "id": countriesIds[country],
      "title": country,
      "image": flags[country],
      "shape": "image",
      "size": 20,
      "color": "black"
  }
  nodesList.push(node)
}

for (let edge of edges) {
  edge.to = countriesIds[edge.to]
}

const nodes = new vis.DataSet(nodesList);
const nodesView = new vis.DataView(nodes);

var options = {
  interaction: {
    zoomSpeed: 0.5,
  },
  nodes: {
    shape: "dot",
    size: 30,
    shadow: {
      enabled: true,
      color: "rgba(0,0,0,0.1)",
      size: 1,
      x: 0,
      y: 4,
    },
  },
  edges: {
    shadow: {
      enabled: false,
      size: 3,
    },
    smooth: {
      forceDirection: "none",
    },
  },
};

onMounted(() => {
  var container = document.getElementById("network");
  var data = {
    nodes: nodesView,
    edges: edges,
  };
   
  var network = new vis.Network(container, data, options);
  network.on('selectNode', (params) => {
    network.focus(params.nodes[0],
        { 
          scale: 2,
          animation: {
            duration: 1000,
            easingFunction: 'easeInOutQuad'
          }
        })

  })

  network.on('deselectNode', (params) => {
      if (params.nodes.length == 0) {
        network.fit(        { 
          scale: 2,
          animation: {
            duration: 1000,
            easingFunction: 'easeInOutQuad'
          }
        })
      }
  })
})

</script>

<template>
  <div id="network" class="visualization">
  </div>
</template>

<style scoped>
.visualization {
  width: 100%;
  height: 100%;
}
</style>
