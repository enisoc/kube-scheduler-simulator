<template>
  <DataTable
    :title="`Pods`"
    :headers="headers"
    :items="pods"
    :on-click="onClick"
  />
</template>

<script lang="ts">
import { V1Pod } from "@kubernetes/client-node";
import { computed, inject, defineComponent } from "@nuxtjs/composition-api";
import DataTable from "./DataTable.vue";
import {} from "../../lib/util";
import PodStoreKey from "../../StoreKey/PodStoreKey";

export default defineComponent({
  components: {
    DataTable,
  },
  setup() {
    const store = inject(PodStoreKey);
    if (!store) {
      throw new Error(`${PodStoreKey} is not provided`);
    }

    const onClick = (pod: V1Pod) => {
      store.select(pod, false);
    };

    const pods = computed(() => {
      return Array<V1Pod>().concat(
        ...Object.values(store.pods).map((p) => {
          return p;
        })
      );
    });
    const search = "";
    const headers = [
      {
        text: "Name",
        value: "metadata.name",
        sortable: true,
      },
      { text: "Namespace", value: "metadata.namespace", sortable: true },
      { text: "Node", value: "spec.nodeName", sortable: true },
      {
        text: "Conditions",
        value: "status.conditions[0].type",
        sortable: true,
      },
      {
        text: "CreationTime",
        value: "metadata.creationTimestamp",
        sortable: true,
      },
      {
        text: "UpdateTime",
        value: "metadata.managedFields[0].time",
        sortable: true,
      },
    ];
    return {
      pods,
      search,
      onClick,
      headers,
    };
  },
});
</script>
