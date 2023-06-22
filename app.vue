<script setup lang="ts">
import { data } from "./data";
const uuid = "A8404194A1875FF3";

function normalize(
  key: "water_SOIL" | "conduct_SOIL" | "temp_SOIL",
  uuid: string
) {
  return {
    data: data
      .filter(
        ({ result }) =>
          result.end_device_ids.dev_eui === uuid &&
          result.uplink_message.decoded_payload &&
          key in result.uplink_message.decoded_payload
      )
      .map(({ result }) => {
        return {
          x: new Date(result.received_at),
          y: parseFloat(result.uplink_message.decoded_payload![key].toString()),
        };
      }),
  };
}

function getDataGraph(
  key: "water_SOIL" | "conduct_SOIL" | "temp_SOIL",
  uuid: string
) {
  const name = () => {
    switch (key) {
      case "water_SOIL":
        return "Humidité du sol";
      case "conduct_SOIL":
        return "Conductivité du sol";
      case "temp_SOIL":
        return "Température du sol";
    }
  };

  return {
    title: {
      text: name(),
    },
    chart: {
      type: "spline",
    },
    xAxis: {
      type: "datetime",
      labels: {
        overflow: "justify",
      },
    },
    yAxis: {
      title: {
        text: name(),
      },
    },
    series: [normalize(key, uuid)],
  };
}
</script>

<template>
  <div>
    <navbar />
    <div class="grid grid-cols-2 gap-2">
      <highchart :options="getDataGraph('water_SOIL', uuid)" />
      <highchart :options="getDataGraph('temp_SOIL', uuid)" />
      <highchart :options="getDataGraph('conduct_SOIL', uuid)" />
    </div>
  </div>
</template>
