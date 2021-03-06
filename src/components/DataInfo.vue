<template>
  <div class="self">
    <div v-if="type === 'hours'">
      Hour of the day
    </div>
    <div v-else-if="type === 'weeks'">
      Week
    </div>
    <div v-else-if="type === 'months'">
      Month
    </div>

    <div class="chart">
      <div v-for="(key, i) in truncatedKeys" :key="key" class="item">
        <span
          class="value"
          :style="{ height: (group[key].count / max) * 100 + '%' }"
          ><span>{{ group[key].count }}</span></span
        >
        <span class="label" v-if="i % 2 === 0">{{ group[key].value }}</span>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
import { Group } from '@/components/DataInstance.vue';

@Component
export default class DataInfo extends Vue {
  @Prop() private group!: Group;
  @Prop() private keys!: string[];
  @Prop() private type!: 'hours' | 'weeks' | 'months';

  get max() {
    return this.keys.reduce(
      (prev, next) => Math.max(this.group[next].count, prev),
      0
    );
  }

  get truncatedKeys() {
    const max = 24;
    if (this.keys.length < max) return this.keys;
    return Array.from(this.keys).splice(
      this.keys.length - max,
      this.keys.length
    );
  }

  formattedHour(hour: number) {
    const date = new Date(0, 0, 0, hour);
    const h = (date.getHours() + 24) % 12 || 12;
    return h + (hour < 12 ? ' AM' : ' PM');
  }
}
</script>

<style scoped>
.chart {
  display: flex;
  height: 120px;
  margin-top: 20px;
  position: relative;
}
.chart .item {
  height: 100%;
  flex: 1;
  font-size: 0.7rem;
  position: relative;
}
.chart .item + .item {
  margin-left: 2px;
}

.chart .item .value {
  background: white;
  bottom: 0;
  display: block;
  position: absolute;
  width: 100%;
}
.chart .item .label {
  top: 100%;
  display: block;
  position: absolute;
  width: 100%;
}
.chart .item .value span {
  bottom: calc(100% + 0px);
  left: 0;
  text-align: center;
  position: absolute;
  width: 100%;
}
</style>
