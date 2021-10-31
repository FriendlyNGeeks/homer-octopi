<template>
  <Generic :item="item">
    <template #content>
      <p class="title is-4">{{ item.name }}</p>
      <p class="subtitle is-6">
        <template v-if="item.subtitle">
          {{ item.subtitle }}
        </template>
        <template v-else-if="api"> Time Elapsed: {{ printtime }} Time Left: {{ printtimeleft }}  </template>
      </p>
    </template>
  </Generic>
</template>

<script>
import service from "@/mixins/service.js";
import Generic from "./Generic.vue";


export default {
  name: "Octopi",
  mixins: [service],
  props: {
    item: Object,
  },
  components: {
    Generic,
  },
  data: () => ({
    api: {
      status: "",
      job: {
        file: {
            display: "",
        },
      },
      progress: {
        state: "",
        printTimeLeft: 0,
        printTime: 0,
      },
    },
  }),
  computed: {
    printtime: function () {
      if (this.api) {
        return (this.api.progress.printTime/60);
      }
    },
    printtimeleft: function () {
      if (this.api) {
        return ((this.api.progress.printTimeLeft / 60)/60);
      }
    },
  },
  created() {
    this.fetchStatus();
  },
  methods: {
    fetchStatus: async function () {
      this.api = await this.fetch("api/job?apikey="+`${this.item.apikey}`).then((response) => response.json()).catch((e) => console.log(e));
    },
  },
};
</script>

<style scoped lang="scss">
.media-left {
  .image {
    display: flex;
    align-items: center;
  }

  img {
    max-height: 100%;
  }
}
.status {
  font-size: 0.8rem;
  color: var(--text-title);

  &.firing:before {
    background-color: #d65c68;
    border-color: #e87d88;
    box-shadow: 0 0 5px 1px #d65c68;
  }

  &.pending:before {
    background-color: #e8bb7d;
    border-color: #d6a35c;
    box-shadow: 0 0 5px 1px #e8bb7d;
  }

  &.inactive:before {
    background-color: #8fe87d;
    border-color: #70d65c;
    box-shadow: 0 0 5px 1px #8fe87d;
  }

  &:before {
    content: " ";
    display: inline-block;
    width: 7px;
    height: 7px;
    margin-right: 10px;
    border: 1px solid #000;
    border-radius: 7px;
  }
}
</style>